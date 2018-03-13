---
title: "Didacticiel : Intégration d’Azure Active Directory à E Sales Manager Remix | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et E Sales Manager Remix."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Didacticiel : Intégration d’Azure Active Directory à E Sales Manager Remix

L’objectif de ce didacticiel est de vous apprendre à intégrer E Sales Manager Remix à Azure Active Directory (Azure AD).

L’intégration d’E Sales Manager Remix à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à E Sales Manager Remix.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à E Sales Manager Remix (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à E Sales Manager Remix, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à E Sales Manager Remix pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’E Sales Manager Remix à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>Ajout d’E Sales Manager Remix à partir de la galerie
Pour configurer l’intégration d’E Sales Manager Remix à Azure AD, vous devez ajouter E Sales Manager Remix à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter E Sales Manager Remix à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez **E Sales Manager Remix**, sélectionnez **E Sales Manager Remix** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![E Sales Manager Remix dans la liste des résultats](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec E Sales Manager Remix, via un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur E Sales Manager Remix équivaut à un utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur E Sales Manager Remix associé doit être établie.

Pour configurer et tester l’authentification unique d’Azure AD avec E Sales Manager Remix, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test E Sales Manager Remix](#create-an-e-sales-manager-remix-test-user)** pour avoir un équivalent de Britta Simon dans E Sales Manager Remix, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure, et configurer l’authentification unique dans votre application E Sales Manager Remix.

**Pour configurer l’authentification unique Azure AD avec E Sales Manager Remix, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **E Sales Manager Remix**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. Dans la section relative aux **domaine et adresses URL E Sales Manager Remix**, procédez comme suit :

    ![Informations d’authentification unique dans la section relative aux domaine et adresses URL E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<Server-Based-URL>/<sub-domain>/`

    c. Copiez la valeur **Identificateur** dans le bloc-notes. Vous utiliserez la valeur Identificateur plus loin dans ce didacticiel.
    
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique d’E Sales Manager Remix](mailto:esupport@softbrain.co.jp).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Sélectionnez **Afficher et modifier tous les autres attributs utilisateur**, puis cliquez sur l’attribut **emailaddress**.
    
    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Copiez la valeur **Espace de noms** et **Nom** de la revendication à partir de la zone de texte. Générez la valeur au format modèle suivant : `<Namespace>/<Name>`. Vous utiliserez cette valeur plus loin dans ce didacticiel.

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. Dans la section relative à la **configuration d’E Sales Manager Remix**, cliquez sur le bouton permettant de **configurer E Sales Manager Remix** pour ouvrir la fenêtre de **configuration de l’authentification unique**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Connectez-vous à votre application E Sales Manager Remix en tant qu’administrateur.

10. Sélectionnez **Menu de l’administrateur** dans le menu en haut à droite.

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Sélectionnez **Paramètres système**>**Coopération avec un système externe**

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Sélectionnez **SAML**

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. Dans la section du **paramètre d’authentification SAML**, procédez comme suit :

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Sélectionnez la **version PC**
    
    b. Sélectionnez **e-mail** dans la liste déroulante de la section des éléments Collaboration.

    c. Dans la zone de texte des éléments Collaboration, collez la **valeur de revendication** que vous avez copiée à partir du portail Azure, par ex. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Dans la zone de texte **Émetteur (ID d’entité)**, collez la valeur **Identificateur** que vous avez copiée à partir du portail Azure dans la section relative aux **domaine et adresses URL E Sales Manager Remix**.

    e. Pour charger le **certificat** téléchargé à partir du portail Azure, sélectionnez **Sélection de fichiers**.

    f. Dans la zone de texte **ID provider login URL** (URL de connexion du fournisseur d’identificateur), collez **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    g. Dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

    h. Cliquez sur **Configuration terminée**

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Créer un utilisateur de test E Sales Manager Remix

1. Connectez-vous à votre application E Sales Manager Remix en tant qu’administrateur.

2. Sélectionnez **Menu de l’administrateur** dans le menu en haut à droite.

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Sélectionnez **Your company settings (Paramètres de votre entreprise)**>**Maintenance of departments and employees (Maintenance des services et des employés)** et sélectionnez **Employees registered** (Employés inscrits).

    ![L’utilisateur](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. Dans la section **New employee registration** (Inscription de nouvel employé), procédez comme suit :
    
    ![L’utilisateur](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. Dans la zone de texte **Employee Name** (Nom de l’employé), tapez le nom d’un utilisateur, par exemple Britta.

    b. Remplissez les champs obligatoires avec les informations de l’utilisateur.
    
    c. Si vous activez SAML, l’administrateur ne sera pas en mesure de se connecter à partir de l’écran de connexion, par conséquent, accordez à l’administrateur des droits de connexion à l’utilisateur en sélectionnant **Admin Login** (Connexion administrateur)

    d. Cliquez sur **Registration** (Inscription)

5. À l’avenir, si vous souhaitez vous connecter en tant qu’administrateur, ouvrez une session avec l’utilisateur disposant de l’autorisation administrateur, puis cliquez sur **Menu de l’administrateur** dans le menu en haut à droite.

    ![L’utilisateur](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique d’Azure en lui accordant l’accès à E Sales Manager Remix.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à E Sales Manager Remix, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **E Sales Manager Remix**.

    ![Lien vers E Sales Manager Remix dans la liste des applications](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque E Sales Manager Remix dans le volet d’accès, vous devez être connecté automatiquement à votre application E Sales Manager Remix.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

