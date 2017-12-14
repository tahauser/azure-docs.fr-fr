---
title: "Didacticiel : Intégration d’Azure Active Directory à FirmPlay - Employee Advocacy for Recruiting | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et FirmPlay - Employee Advocacy for Recruiting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: e1381f2273ee961a4b72dede8cf8e017814ba909
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Didacticiel : Intégration d’Azure Active Directory à FirmPlay - Employee Advocacy for Recruiting

Dans ce didacticiel, vous allez apprendre à intégrer FirmPlay - Employee Advocacy for Recruiting à Azure Active Directory (Azure AD).

L’intégration de FirmPlay - Employee Advocacy for Recruiting dans Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à FirmPlay - Employee Advocacy for Recruiting.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FirmPlay - Employee Advocacy for Recruiting (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le Portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à FirmPlay - Employee Advocacy for Recruiting, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FirmPlay - Employee Advocacy for Recruiting pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FirmPlay - Employee Advocacy for Recruiting à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Ajout de FirmPlay - Employee Advocacy for Recruiting à partir de la galerie
Pour configurer l’intégration de FirmPlay - Employee Advocacy for Recruiting dans Azure AD, vous devez ajouter FirmPlay - Employee Advocacy for Recruiting à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter FirmPlay - Employee Advocacy for Recruiting à partir de la galerie, procédez comme suit :**

1. Dans le **[Portail de gestion Azure](https://portal.azure.com)**, dans le panneau de navigation gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **FirmPlay - Employee Advocacy for Recruiting**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. Dans le volet de résultats, sélectionnez **FirmPlay - Employee Advocacy for Recruiting**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec FirmPlay - Employee Advocacy for Recruiting avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur FirmPlay - Employee Advocacy for Recruiting équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur FirmPlay - Employee Advocacy for Recruiting associé doit être établie.

Pour ce faire, affectez la valeur du champ **nom d’utilisateur** d’Azure AD comme valeur de **nom d’utilisateur** dans FirmPlay - Employee Advocacy for Recruiting.

Pour configurer et tester l’authentification unique Azure AD avec FirmPlay - Employee Advocacy for Recruiting, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test FirmPlay - Employee Advocacy for Recruiting](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)** pour disposer d’un équivalent de Britta Simon dans FirmPlay - Employee Advocacy for Recruiting associé à sa représentation Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application FirmPlay - Employee Advocacy for Recruiting.

**Pour configurer l’authentification unique d’Azure AD avec FirmPlay - Employee Advocacy for Recruiting, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **FirmPlay - Employee Advocacy for Recruiting**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. Dans la section **FirmPlay - Employee Advocacy for Recruiting Domain and URLs** (Domaine et URL FirmPlay - Employee Advocacy for Recruiting), dans la zone de texte **URL de connexion**, entrez une URL respectant le modèle suivant : `https://<your-subdomain>.firmplay.com/`

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe de support de FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) pour obtenir cette valeur. 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (base64)** puis enregistrez le fichier du certificat sur votre ordinateur. 

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. Dans la section **FirmPlay - Employee Advocacy for Recruiting Configuration** (Configuration de FirmPlay - Employee Advocacy for Recruiting), cliquez sur **Configure FirmPlay - Employee Advocacy for Recruiting** (Configurer FirmPlay - Employee Advocacy for Recruiting) pour ouvrir la boîte de dialogue **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Pour obtenir la configuration de l’authentification unique pour votre application, contactez [l’équipe de support de FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) et fournissez-lui les éléments suivants : 

    •  Le **fichier de certificat** téléchargé

    •  L’**URL du service d’authentification unique SAML**

    •  L’**ID d’entité SAML**

    •  L’**URL de déconnexion**
  

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Création d’un utilisateur de test FirmPlay - Employee Advocacy for Recruiting

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans FirmPlay - Employee Advocacy for Recruiting. Contactez [l’équipe de support de FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) pour ajouter les utilisateurs à la plateforme FirmPlay - Employee Advocacy for Recruiting.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FirmPlay - Employee Advocacy for Recruiting.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à FirmPlay - Employee Advocacy for Recruiting, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **FirmPlay - Employee Advocacy for Recruiting**.

    ![Configurer l’authentification unique](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque FirmPlay - Employee Advocacy for Recruiting dans le volet d’accès, vous devez être connecté automatiquement à votre application FirmPlay - Employee Advocacy for Recruiting.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png