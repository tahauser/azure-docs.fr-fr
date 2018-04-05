---
title: 'Didacticiel : Intégration d’Azure Active Directory avec SafetyNet | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SafetyNet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: caa96ea2-da21-4529-8fab-0e06367beb40
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: jeedes
ms.openlocfilehash: b304a198ce1791a14094eea4cad3b6de870c80ce
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-safetynet"></a>Didacticiel : Intégration d’Azure Active Directory à SafetyNet

L’objectif de ce didacticiel est de vous apprendre à intégrer SafetyNet à Azure Active Directory (Azure AD).

L’intégration de SafetyNet dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SafetyNet.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SafetyNet (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à SafetyNet, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SafetyNet pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SafetyNet à partir de la gallerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-safetynet-from-the-gallery"></a>Ajout de SafetyNet à partir de la gallerie
Pour configurer l’intégration de SafetyNet à Azure AD, vous devez ajouter SafetyNet, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SafetyNet à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SafetyNet**, sélectionnez **SafetyNet** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SafetyNet dans la liste des résultats](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SafetyNet avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SafetyNet équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SafetyNet associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SafetyNet, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test SafetyNet](#create-a-safetynet-test-user)** pour avoir un équivalent de Britta Simon dans SafetyNet lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SafetyNet.

**Pour configurer l’authentification unique Azure AD avec SafetyNet, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **SafetyNet**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_samlbase.png)

3. Dans la section **Domaines et URL SafetyNet**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL SafetyNet](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.predictivesolutions.com/sp`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.predictivesolutions.com/CRMApp/saml/SSO`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL SafetyNet](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.predictivesolutions.com`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SafetyNet](mailto:dev@predictivesolutions.com). 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-safetynet-tutorial/tutorial_general_400.png)

6. Pour générer l’**URL des métadonnées**, effectuez les étapes suivantes :

    a. Cliquez sur **Inscriptions des applications**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_appregistrations.png)
   
    b. Cliquez sur **Points de terminaison** pour ouvrir la boîte de dialogue **Points de terminaison**.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_endpointicon.png)

    c. Cliquez sur le bouton Copier pour copier l’URL du document de métadonnées de fédération (**FEDERATION METADATA DOCUMENT**), puis collez-la dans le Bloc-notes.
    
    ![Configure Single Sign-On](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_endpoint.png)
     
    d. Accédez maintenant à la page de propriétés de **SafetyNet**, puis copiez **l’ID d’application** à l’aide du bouton **Copier** et collez-le dans le Bloc-notes.
 
    ![Configure Single Sign-On](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_appid.png)

    e. Générez l’**URL des métadonnées** en utilisant le modèle suivant : `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.
    
7. Pour configurer l’authentification unique côté **SafetyNet**, vous devez envoyer l’**URL de métadonnées** générée à l’[équipe de support SafetyNet](mailto:dev@predictivesolutions.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-safetynet-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-safetynet-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-safetynet-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-safetynet-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-safetynet-test-user"></a>Créer un utilisateur de test SafetyNet

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SafetyNet. Collaborez avec l’[équipe du support technique SafetyNet](mailto:dev@predictivesolutions.com) pour ajouter des utilisateurs dans la plate-forme SafetyNet. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SafetyNet.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SafetyNet, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SafetyNet**.

    ![Lien SafetyNet dans la liste des applications](./media/active-directory-saas-safetynet-tutorial/tutorial_safetynet_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque SafetyNet dans le volet d’accès, vous devez être connecté automatiquement à votre application SafetyNet.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-safetynet-tutorial/tutorial_general_203.png

