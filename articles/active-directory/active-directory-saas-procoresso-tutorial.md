---
title: "Didacticiel : Intégration d’Azure Active Directory à Procore SSO | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Procore SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: c6bae8e24f252a535571308467b428832b542e48
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Didacticiel : Intégration d’Azure Active Directory à Procore SSO

Dans ce didacticiel, vous allez apprendre à intégrer Procore SSO à Azure Active Directory (Azure AD).

L’intégration de Procore SSO dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Procore SSO
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Procore SSO par le biais de l’authentification unique (SSO) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Procore SSO, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Procore SSO.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Procore SSO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Procore SSO pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Procore SSO à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-procore-sso-from-the-gallery"></a>Ajout de Procore SSO à partir de la galerie
Pour configurer l’intégration de Procore SSO à Azure AD, vous devez ajouter Procore SSO à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Procore SSO à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans le champ de recherche, tapez **Procore SSO**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. Dans le volet de résultats, sélectionnez **Procore SSO**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Procore SSO avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Procore SSO équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Procore SSO associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** dans Procore SSO.

Pour configurer et tester l’authentification unique Azure AD avec Procore SSO, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Procore SSO](#creating-a-procore-sso-test-user)** pour avoir un équivalent de Britta Simon dans Procore SSO lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Procore SSO.

**Pour configurer l’authentification unique Azure AD avec Procore SSO, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Procore SSO**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Dans la section **Domaine SSO et URL de Procore**, l’utilisateur n’aura pas à effectuer les étapes que l’application a déjà intégrées préalablement avec Azure.

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Procore SSO** , cliquez sur **Configurer PProcore SSO** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Pour configurer l’authentification unique sur **Procore SSO**, connectez-vous à votre site d’entreprise Procore en tant qu’administrateur.

8. Dans la liste déroulante de boîte à outils, cliquez sur **Admin** pour ouvrir la page de paramètres de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Collez les valeurs dans les zones comme décrit ci-dessous-

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. Dans la zone **Émetteur d’URL d’authentification unique**, collez l’ID d’entité SAML copiée à partir du portail Azure.

    b. Dans la zone **URL cible de connexion SAML**, collez l’URL d’authentification unique SAML copiée à partir du portail Azure.

    c. Ouvrez maintenant le fichier **XML de métadonnées** téléchargé à partir du portail Azure et copiez le certificat dans la balise nommée **X509Certificate**. Collez la valeur copiée dans la zone **Certificat x509 d’authentification unique**.

10. Cliquez sur **Enregistrer les modifications**.

11. Après ces paramètres, vous devez envoyer le **nom de domaine** (par ex. **contoso.com**) via lequel vous connectez à Procore à [l’équipe de support technique de Procore](https://support.procore.com/), qui activera l’authentification unique fédérée pour ce domaine.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-procore-sso-test-user"></a>Création d’un utilisateur de test Procore SSO

Suivez les étapes ci-dessous pour créer un utilisateur de test Procore de leur côté.

1. Connectez-vous à votre site d’entreprise Procore en tant qu’administrateur.  

2. Dans la liste déroulante de boîte à outils, cliquez sur **Annuaire** pour ouvrir la page d’annuaire de l’entreprise.

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Cliquez sur l’option **Ajouter une personne** pour ouvrir le formulaire et réglez les options suivantes -

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. Dans la zone de texte **First Name**, tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Adresse e-mail** , tapez l’adresse de messagerie de l’utilisateur, par exemple **BrittaSimon@contoso.com**.

    d. Sélectionnez **Modèle d’autorisation** pour **Appliquer le modèle d’autorisation plus tard**.

    e. Cliquez sur **Create**.

4. Vérifiez et mettez à jour les détails du contact nouvellement ajouté.

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Cliquez sur **Enregistrer et envoyer l’invitation** (si une invitation par e-mail est requise) ou **Enregistrer** (enregistrer directement) pour terminer l’inscription de l’utilisateur.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Procore SSO.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Procore SSO, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Procore SSO**.

    ![Configurer l’authentification unique](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). Lorsque vous cliquez sur la mosaïque Procore SSO dans le volet d’accès, vous devez être connecté automatiquement à votre application Procore SSO.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

