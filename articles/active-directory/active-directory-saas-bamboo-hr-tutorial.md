---
title: "Didacticiel : Intégration d’Azure Active Directory à BambooHR | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et BambooHR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 081144a645683d4d00ed0d464e23558378dc1b38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Didacticiel : Intégration d’Azure Active Directory à BambooHR

Dans ce didacticiel, vous allez apprendre à intégrer BambooHR à Azure Active Directory (Azure AD).

L’intégration de BambooHR à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à BambooHR.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à BambooHR par le biais de l’authentification unique (SSO) avec leur compte Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à BambooHR, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement BambooHR avec l’authentification unique (SSO) activée

> [!NOTE]
> Nous déconseillons d’utiliser un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de BambooHR à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-bamboohr-from-the-gallery"></a>Ajout de BambooHR à partir de la galerie
Pour configurer l’intégration de BambooHR à Azure AD, ajoutez BambooHR à partir de la galerie à votre liste d’applications SaaS managées en effectuant les actions suivantes :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise][2]
    
3. Pour ajouter une application, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **BambooHR**. Dans la liste des résultats, sélectionnez **BambooHR**, puis **Ajouter**.

    ![BambooHR dans la liste des résultats](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique (SSO) Azure AD avec BambooHR en utilisant l’utilisateur de test « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître son utilisateur équivalent dans BambooHR. En d’autres termes, vous devez établir un lien entre l’utilisateur Azure AD et l’utilisateur associé dans BambooHR.

Pour établir ce lien dans BambooHR, la valeur du **nom d’utilisateur** BambooHR doit être la même que celle du **nom d’utilisateur** Azure AD.

Pour configurer et tester l’authentification unique Azure AD avec BambooHR, suivez les étapes de base des cinq sections suivantes.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique (SSO) Azure AD dans le portail Azure et la configurez dans votre application BambooHR en effectuant les actions suivantes :

1. Dans le portail Azure, dans la page d’intégration de l’application **BambooHR**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la fenêtre **Authentification unique**, dans la liste déroulante **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Fenêtre Authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Sous **Domaine et URL BambooHR**, effectuez les actions suivantes :

    ![Section Domaine et URL BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://<company>.bamboohr.com`.

    b. Dans la zone **Identificateur**, tapez une valeur : `BambooHR-SAML`.

    > [!NOTE] 
    > La valeur du champ **URL de connexion** n’est pas réelle. Mettez-la à jour avec votre URL de connexion réelle. Pour obtenir la valeur, contactez [l’équipe du support client de BambooHR](https://www.bamboohr.com/contact.php). 
 
4. Sous **Certificat de signature SAML**, sélectionnez **Certificat (en base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. Sous **Configuration de BambooHR**, sélectionnez **Configurer BambooHR** pour ouvrir la fenêtre **Configurer l’authentification**. Dans la section **Référence rapide**, copiez **l’URL du service d’authentification unique SAML** pour l’utiliser plus tard.

    ![Configuration de BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. Dans une nouvelle fenêtre, connectez-vous à votre site d’entreprise BambooHR comme administrateur.

8. Dans la page d’accueil, effectuez les actions suivantes :
   
    ![Page Authentification unique BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Authentification unique")   

    a. Sélectionnez **Applications**.
   
    b. Dans le volet **Applications**, sélectionnez **Authentification unique**.
   
    c. Sélectionnez **Authentification unique SAML**.

9. Dans le volet **Authentification unique SAML**, effectuez les actions suivantes :
   
    ![Volet Authentification unique SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Authentification unique SAML")
   
    a. Dans la zone **URL de connexion SSO**, collez **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure à l’étape 6.
      
    b. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat X.509**.
   
    c. Sélectionnez **Enregistrer**.

> [!TIP]
> Pendant que vous configurez l’application, vous pouvez lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com). Après avoir ajouté l’application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée via la section **Configuration** en bas. Pour plus d’informations, consultez [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer l’utilisateur de test Azure AD Britta Simon][100]

Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. En haut du volet **Tous les utilisateurs**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. Dans la fenêtre **Utilisateur**, suivez les étapes ci-dessous :

    ![Fenêtre Utilisateur](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-bamboohr-test-user"></a>Créer un utilisateur de test BambooHR

Pour permettre aux utilisateurs Azure AD de se connecter à BambooHR, configurez-les manuellement dans BambooHR en effectuant les actions suivantes :

1. Connectez-vous à votre site **BambooHR** comme administrateur.

2. Dans la barre d’outils en haut, sélectionnez **Paramètres**.
   
    ![Bouton Paramètres](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Paramètres")

3. Sélectionnez **Vue d’ensemble**.

4. Dans le volet gauche, sélectionnez **Sécurité** > **Utilisateurs**.

5. Tapez le nom d’utilisateur, le mot de passe et l’adresse e-mail du compte Azure AD valide que vous voulez configurer.

6. Sélectionnez **Enregistrer**.
        
>[!NOTE]
>Pour configurer des comptes d’utilisateur Azure AD, vous pouvez aussi utiliser les outils ou API de création de compte d’utilisateur de BambooHR.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BambooHR.

![Attribuer le rôle utilisateur][200] 

Pour affecter l’utilisateur Britta Simon à BambooHR, effectuez les actions suivantes :

1. Dans le portail Azure, ouvrez la vue Applications, accédez à la vue Répertoire, puis sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste **Applications d’entreprise**, sélectionnez **BambooHR**.

    ![Lien BambooHR dans la liste Applications d’entreprise](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter** et, dans le volet **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la liste **Utilisateurs** de la fenêtre **Utilisateurs et groupes**, sélectionnez **Britta Simon**.

6. Cliquez sur le bouton **Sélectionner**.

7. Dans la fenêtre **Ajouter une affectation**, sélectionnez le bouton **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Testez votre configuration SSO Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette **BambooHR** dans le volet d’accès, vous devez être automatiquement connecté à votre application BambooHR.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

