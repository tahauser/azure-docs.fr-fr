---
title: 'Didacticiel : IntégrerAzure Active Directory à E Sales Manager Remix | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et E Sales Manager Remix.
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
ms.openlocfilehash: 200d87b950ac76c85513bc11da2798562e48bec3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Intégrer Azure Active Directory à E Sales Manager Remix

L’objectif de ce didacticiel est de vous apprendre à intégrer Azure Active Directory (Azure AD) à E Sales Manager Remix.

L’intégration d’Azure AD à E Sales Manager Remix vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à E Sales Manager Remix.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à E Sales Manager Remix (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à E Sales Manager Remix, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à E Sales Manager Remix pour lequel l’authentification unique est activée

> [!NOTE]
> Lorsque vous testez les étapes de ce didacticiel, nous *déconseillons* l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout d’E Sales Manager Remix à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Ajouter E Sales Manager Remix à partir de la galerie
Pour configurer l’intégration d’Azure AD à E Sales Manager Remix, ajoutez E Sales Manager Remix à votre liste d’applications SaaS gérées, à partir de la galerie, de cette façon :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Fenêtre « Applications d’entreprise »][2]
    
3. Pour ajouter une application, en haut de la fenêtre, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **E Sales Manager Remix**, sélectionnez **E Sales Manager Remix** dans la liste des résultats, puis **Ajouter**.

    ![E Sales Manager Remix dans la liste des résultats](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec E Sales Manager Remix, via un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit identifier l’utilisateur E Sales Manager Remix et son équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur E Sales Manager Remix associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec E Sales Manager Remix, suivez les indications des sections dans les cinq sections suivantes :

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Activez l’authentification unique Azure AD dans le portail Azure, et configurer l’authentification unique dans votre application E Sales Manager Remix, en procédant comme suit :

1. Dans le portail Azure, sur la page d’intégration de l’application **E Sales Manager Remix**, sélectionnez **Authentification unique**.

    ![Lien « Authentification unique »][4]

2. Dans la zone **Mode d’authentification unique** de la fenêtre **Authentification unique**, sélectionnez **Authentification SAML**.
 
    ![Fenêtre « Authentification unique »](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. Sous **Domaine et URL E Sales Manager Remix**, procédez comme suit :

    ![Informations d’authentification unique dans la section relative aux domaine et adresses URL E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Dans le champ **URL d’authentification**, entrez une URL dans le format suivant : *https://\<Server-Based-URL>/\<sub-domain>/esales-pc*.

    b. Dans le champ **Identificateur**, entrez une URL dans le format suivant : *https://\<Server-Based-URL>/\<sub-domain>/*.

    c. Notez la valeur **Identificateur** en vue d’une utilisation plus tard dans ce didacticiel.
    
    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas réelles. Mettez à jour les valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique d’E Sales Manager Remix](mailto:esupport@softbrain.co.jp).

4. Sous **Certificat de signature SAML**, sélectionnez **Certificat (en base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat (en base64)](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Cochez la case **Afficher et modifier tous les autres attributs utilisateur**, puis sélectionnez l’attribut **emailaddress**.
    
    ![Fenêtre Attributs de l’utilisateur](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

    La fenêtre **Modifier l’attribut** s’affiche.

6. Copiez les valeurs **Espace de noms** et **Nom**. Générez la valeur dans le modèle *\<Namespace>/\<Name>*, et enregistrez-la en vue d’une utilisation plus tard dans ce didacticiel.

    ![Fenêtre Modifier l’attribut](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. Sous **Configuration E Sales Manager Remix**, sélectionnez **Configurer E Sales Manager Remix**.

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    La fenêtre **Configurer l’authentification** s’affiche.

8. Dans la section **Référence rapide**, copiez l’URL de déconnexion et l’URL du service d’authentification unique SAML.

9. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

10. Connectez-vous à votre application E Sales Manager Remix en tant qu’administrateur.

11. Sélectionnez **Menu de l’administrateur** en haut à droite.

    ![Commande « Menu de l’administrateur »](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

12. Dans le volet de gauche, sélectionnez **Paramètres système** > **Coopération avec un système externe**.

    ![Liens « Paramètres système » et « Coopération avec un système externe »](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
13. Dans la fenêtre **Coopération avec un système externe**, sélectionnez **SAML**.

    ![Fenêtre « Coopération avec un système externe »](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

14. Sous le **Paramètre d’authentification SAML**, procédez comme suit :

    ![Section « Paramètre d’authentification SAML »](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Cochez la case **Version PC**.
    
    b. Dans la section **Éléments Collaboration**, dans la liste déroulante, sélectionnez **e-mail**.

    c. Dans le champ **Éléments Collaboration**, collez la valeur de revendication que vous avez copiée plus tôt dans le portail Azure (**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. Dans le champ **Émetteur (ID d’entité)**, collez la valeur Identificateur que vous avez copiée plus tôt à partir de la section relative aux **domaine et adresses URL E Sales Manager Remix** du portail Azure.

    e. Pour charger le certificat téléchargé à partir du portail Azure, sélectionnez **Sélection de fichiers**.

    f. Dans le champ **URL de connexion du fournisseur d’identité**, collez l’URL du service d’authentification unique SAML que vous avez copiée plus tôt dans le portail Azure.

    g. Dans le champ **URL de déconnexion du fournisseur d’identité**, collez la valeur de l’URL de déconnexion que vous avez copiée plus tôt dans le portail Azure.

    h. Sélectionnez **Configuration terminée**.

> [!TIP]
> Au moment de configurer l’application, vous pouvez lire une version abrégée des instructions précédentes sur le [Portail Azure](https://portal.azure.com). Après avoir ajouté l’application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**, puis accédez à la documentation intégrée dans la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez la [documentation incorporée d’Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure en procédant comme suit :

![Créer un utilisateur de test Azure AD][100]

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Lien Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Pour afficher une liste des utilisateurs actuels, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. En haut de la fenêtre **Tous les utilisateurs**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    La fenêtre **Utilisateur** s’ouvre.

4. Dans la fenêtre **Utilisateur**, suivez les étapes ci-dessous :

    ![Fenêtre Utilisateur](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Créer un utilisateur de test E Sales Manager Remix

1. Connectez-vous à votre application E Sales Manager Remix en tant qu’administrateur.

2. Sélectionnez **Menu de l’administrateur** dans le menu en haut à droite.

    ![Configuration d’E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Sélectionnez **Paramètres de votre entreprise** > **Maintenance des services et des employés** et sélectionnez **Employés inscrits**.

    ![Onglet « Employés inscrits »](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. Dans la section **Inscription de nouvel employé**, procédez comme suit :
    
    ![Section « Inscription de nouvel employé »](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. Dans le champs **Nom de l’employé**, entrez le nom de l’utilisateur (par exemple, **Britta**).

    b. Renseignez les champs nécessaires restants.
    
    c. Si vous activez SAML, l’administrateur ne peut pas se connecter depuis la page de connexion. Donnez à l’utilisateur des privilèges de connexion d’administrateur en cochant la case **Connexion d’administrateur**.

    d. Sélectionnez **Inscription**.

5. À l’avenir, pour vous connecter en tant qu’administrateur, connectez-vous avec l’utilisateur disposant des autorisations d’administrateur puis, dans le coin supérieur droit, sélectionnez **Menu de l’administrateur**.

    ![Commande « Menu de l’administrateur »](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique d’Azure en lui accordant l’accès à E Sales Manager Remix. Pour ce faire, procédez comme suit : 

![Attribuer le rôle utilisateur][200] 

1. Sur le Portail Azure, ouvrez la vue **Applications**, accédez à la vue **Répertoire**, puis sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Liens « Applications d’entreprise » et « Toutes les applications »][201] 

2. Dans la liste des **applications**, sélectionnez **E Sales Manager Remix**.

    ![Lien E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez **Ajouter** puis, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la liste **Utilisateurs** de la fenêtre **Utilisateurs et groupes**, sélectionnez **Britta Simon**.

6. Cliquez sur le bouton **Sélectionner**.

7. Dans la fenêtre **Ajouter une affectation**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

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

