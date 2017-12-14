---
title: "Didacticiel : intégration d’Azure Active Directory à Teamphoria | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Teamphoria."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 260c85b14032e17def01ded4a461e6337d66239b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Didacticiel : Intégration d’Azure Active Directory à Teamphoria

Dans ce didacticiel, vous allez apprendre à intégrer Teamphoria à Azure Active Directory (Azure AD).

L’intégration de Teamphoria dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Teamphoria
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Teamphoria (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Teamphoria, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Teamphoria pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Teamphoria à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-teamphoria-from-the-gallery"></a>Ajout de Teamphoria à partir de la galerie
Pour configurer l’intégration de Teamphoria à Azure AD, vous devez ajouter Teamphoria à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Teamphoria à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Teamphoria**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. Dans le panneau de résultats, sélectionnez **Teamphoria**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Teamphoria avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Teamphoria équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Teamphoria associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** dans Teamphoria.

Pour configurer et tester l’authentification unique Azure AD avec Teamphoria, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Teamphoria](#creating-a-teamphoria-test-user)** pour avoir un équivalent de Britta Simon dans Teamphoria qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail de gestion Azure et configurer l’authentification unique dans votre application Teamphoria.

**Pour configurer l'authentification unique Azure AD avec Teamphoria, procédez comme suit :**

1. Dans le Portail de gestion Azure, sur la page d’intégration de l’application **Teamphoria**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Dans la section **Domaine et URL Teamphoria**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL au format suivant : `https://<sub-domain>.teamphoria.com/login`    

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion réelle. Contactez l[’équipe de support technique Teamphoria](https://www.teamphoria.com/) pour obtenir l’URL de connexion. 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Teamphoria**, cliquez sur **Configurer Teamphoria** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. Pour configurer l’authentification unique côté **Teamphoria**, connectez-vous à votre application Teamphoria en tant qu’administrateur.

8. Accédez à l’option **ADMIN SETTINGS** (Paramètres d’administration) dans la barre d’outils gauche puis, sous l’onglet Configure (Configurer), cliquez sur **SINGLE SIGN-ON** (Authentification unique) pour ouvrir la fenêtre de configuration de l’authentification unique (SSO).

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Cliquez sur l’option **ADD NEW IDENTITY PROVIDER** (Ajouter un nouveau fournisseur d’identité) dans le coin supérieur droit pour ouvrir le formulaire permettant d’ajouter les paramètres pour l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Entrez les informations dans les champs comme indiqué ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME** (Nom d’affichage) : entrez le nom d’affichage du plug-in sur la page d’administration.

    b. **BUTTON NAME** (Nom du bouton) : nom de l’onglet qui s’affiche sur la page de connexion via l’authentification unique.

    c. **CERTIFICATE** (Certificat) : ouvrez le certificat téléchargé précédemment à partir du portail Azure dans le Bloc-notes, copiez le contenu puis collez-le dans ce champ.

    d. **ENTRY POINT** (Point d’entrée) : collez l**’URL du service d’authentification unique SAML** copiée précédemment à partir du portail Azure.

    e. Définissez l’option sur **ON** , puis cliquez sur **SAVE** (Enregistrer).   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-teamphoria-test-user"></a>Création d’un utilisateur de test Teamphoria

Pour se connecter à Teamphoria, les utilisateurs d’Azure AD doivent être approvisionnés dans Teamphoria. Dans le cas de Teamphoria, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Teamphoria en tant qu’administrateur.

2. Cliquez sur les paramètres **ADMIN** dans la barre d’outils gauche puis, sous l’onglet **MANAGE** (Gérer), cliquez sur **USERS** (Utilisateurs) afin d’ouvrir la page d’administration pour les utilisateurs.

    ![Ajouter un employé](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Cliquez sur l’option **MANUAL INVITE** (Inviter manuellement).

    ![Inviter des personnes](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. Dans cette page, effectuez l’action suivante. 
    
    ![Inviter des personnes](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. Dans la zone de texte **EMAIL ADDRESS** (Adresse e-mail), tapez l’**adresse de messagerie** de Britta Simon.

    b. Dans la zone de texte **FIRST NAME** (Prénom), tapez **Britta**.

    c. Dans la zone de texte **LAST NAME** (Nom), tapez **Simon**.

    d. Cliquez sur **INVITE 1 USER** (Inviter l’utilisateur 1). L’utilisateur doit accepter l’invitation pour être créé dans le système.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Teamphoria.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Teamphoria, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Teamphoria**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png

