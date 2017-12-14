---
title: "Didacticiel : Intégration d’Azure Active Directory à Zscaler Private Access (ZPA) | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler Private Access (ZPA)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 9ac65791e8d50d5e5da5d36adee14cd0e88a3da8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Didacticiel : Intégration d’Azure Active Directory à Zscaler Private Access (ZPA)

Dans ce didacticiel, vous allez apprendre à intégrer Zscaler Private Access (ZPA) à Azure Active Directory (Azure AD).

L’intégration de Zscaler Private Access (ZPA) dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Zscaler Private Access (ZPA)
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Zscaler Private Access (ZPA) (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Zscaler Private Access (ZPA), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Zscaler Private Access (ZPA) activé via l’authentification unique (SSO)


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Zscaler Private Access (ZPA) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Ajout de Zscaler Private Access (ZPA) à partir de la galerie
Pour configurer l’intégration de Zscaler Private Access (ZPA) à Azure AD, vous devez ajouter Zscaler Private Access (ZPA) à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Zscaler Private Access (ZPA) à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Zscaler Private Access (ZPA)**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. Dans le volet de résultats, sélectionnez **Zscaler Private Access (ZPA)**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zscaler Private Access (ZPA) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Zscaler Private Access (ZPA) équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Zscaler Private Access (ZPA) associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Zscaler Private Access (ZPA).

Pour configurer et tester l'authentification unique Azure AD avec Zscaler Private Access (ZPA), vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d'un utilisateur de test Zscaler Private Access (ZPA)](#creating-a-zscaler-private-access-(zpa)-test-user)** pour avoir un équivalent de Zscaler Private Access (ZPA) Simon dans Moxtra lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Zscaler Private Access (ZPA).

**Pour configurer l’authentification unique Azure AD avec Zscaler Private Access (ZPA), procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Zscaler Private Access (ZPA)**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. Dans la section **Domaine et URL Zscaler Private Access (ZPA)**, procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. Dans la zone de texte **Identificateur**, tapez : `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Nous vous suggérons d’utiliser ici la valeur d’URL unique dans l’identificateur. Contactez [l’équipe de support technique Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) pour obtenir ces valeurs.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)   

5. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler Private Access (ZPA) en tant qu’administrateur.

10. Accédez à **Administrateur**, puis cliquez sur **Configuration Idp**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. Dans la section **Configuration Idp**, cliquez sur **Ajouter une nouvelle configuration IDP**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. Dans la section **Nouvelle configuration IDP**, procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Cliquez sur **Sélectionner un fichier** et chargez votre fichier de métadonnées téléchargé.

    b. Cliquez sur le bouton **Enregistrer** .
    


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Création d’un utilisateur de test Zscaler Private Access (ZPA)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Zscaler Private Access (ZPA). Collaborez avec [l’équipe de support technique Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) pour ajouter les utilisateurs dans la plate-forme Zscaler Private Access (ZPA).


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler Private Access (ZPA).

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Zscaler Private Access (ZPA), procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Zscaler Private Access (ZPA)**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Zscaler Private Access (ZPA) dans le volet d'accès, vous êtes connecté automatiquement à votre application Zscaler Private Access (ZPA).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png