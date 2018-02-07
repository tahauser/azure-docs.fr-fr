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
ms.openlocfilehash: 6582a0b05539f322801273374d7c8fbccfe2da60
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Didacticiel : Intégration d’Azure Active Directory à BambooHR

Dans ce didacticiel, vous allez apprendre à intégrer BambooHR à Azure Active Directory (Azure AD).

L’intégration de BambooHR à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à BambooHR.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à BambooHR (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>configuration requise

Pour configurer l’intégration d’Azure AD à BambooHR, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement BambooHR pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de BambooHR à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-bamboohr-from-the-gallery"></a>Ajout de BambooHR à partir de la galerie
Pour configurer l’intégration de BambooHR à Azure AD, vous devez ajouter BambooHR, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter BambooHR à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **BambooHR**, sélectionnez **BambooHR** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![BambooHR dans la liste des résultats](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec BambooHR, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur BambooHR correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur BambooHR associé.

Dans BambooHR, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec BambooHR, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test BambooHR](#create-a-bamboohr-test-user)** pour avoir un équivalent de Britta Simon dans BambooHR lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application BambooHR.

**Pour configurer l’authentification unique Azure AD avec BambooHR, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **BambooHR**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Dans la section **Domaine et URL BambooHR**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company>.bamboohr.com`

    b. Dans la zone de texte **Identificateur**, tapez une valeur : `BambooHR-SAML`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir la valeur, contactez [l’équipe de support de BambooHR](https://www.bamboohr.com/contact.php). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de BambooHR**, cliquez sur **Configurer BambooHR** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise BambooHR en tant qu’administrateur.

7. Dans la page d’accueil, procédez comme suit :
   
    ![Authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Authentification unique")   

    a. Cliquez sur **Applications**.
   
    b. Dans le menu des applications sur la gauche, cliquez sur **Single Sign-On**.
   
    c. Cliquez sur **SAML Single Sign-On**.

8. Dans la section **SAML Single Sign-On** , procédez comme suit :
   
    ![Authentification unique SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Authentification unique SAML")
   
    a. Dans la zone de texte **SSO Login Url** (URL de connexion avec authentification unique), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
      
    b. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.
   
    c. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-bamboohr-test-user"></a>Créer un utilisateur de test BambooHR

Pour permettre aux utilisateurs Azure AD de se connecter à BambooHR, vous devez les attribuer dans BambooHR.  

En l’occurrence, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site **BambooHR** en tant qu’administrateur.

2. Dans la barre d’outils située en haut, cliquez sur **Settings**.
   
    ![Paramètre](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Paramètre")

3. Cliquez sur **Overview**.

4. Dans le volet de navigation de gauche, accédez à **Security \> Users**.

5. Tapez le nom d’utilisateur, le mot de passe et l’adresse e-mail d’un compte AAD valide que vous souhaitez attribuer dans les zones de texte correspondantes.

6. Cliquez sur **Enregistrer**.
        
>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par BambooHR pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BambooHR.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à BambooHR, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **BambooHR**.

    ![Lien BambooHR dans la liste des applications](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette BambooHR dans le volet d’accès, vous devez être connecté automatiquement à votre application BambooHR.
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

