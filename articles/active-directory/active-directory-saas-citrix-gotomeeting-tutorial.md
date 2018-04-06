---
title: 'Didacticiel : Intégration d’Azure Active Directory à GoToMeeting | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: d26b78fb5be96e979fb7b375acf6e907d858b706
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Didacticiel : Intégration d’Azure Active Directory à GoToMeeting

Dans ce didacticiel, vous allez apprendre à intégrer GoToMeeting à Azure Active Directory (Azure AD).

L’intégration de GoToMeeting à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à GoToMeeting.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à GoToMeeting (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à GoToMeeting, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement GoToMeeting pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de GoToMeeting à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-gotomeeting-from-the-gallery"></a>Ajout de GoToMeeting à partir de la galerie
Pour configurer l’intégration de GoToMeeting à Azure AD, vous devez ajouter GoToMeeting, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter GoToMeeting à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **GoToMeeting**, sélectionnez **GoToMeeting** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![GoToMeeting dans la liste des résultats](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec GoToMeeting avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur GoToMeeting équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur GoToMeeting associé.

Dans GoToMeeting, attribuez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec GoToMeeting, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test GoToMeeting](#create-a-gotomeeting-test-user)** pour avoir un équivalent de Britta Simon dans GoToMeeting, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application GoToMeeting.

**Pour configurer l’authentification unique Azure AD avec GoToMeeting, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **GoToMeeting**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. Dans la section **Domaine et URL GoToMeeting**, effectuez les étapes suivantes :

    ![Informations d’authentification unique Domaine et URL GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_url.png)

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://authentication.logmeininc.com/saml/sp`

4. Cliquez sur **Afficher la configuration de l’URL avancée** et configurez les URM ci-dessous

    **URL de connexion** (gardez cela vide)
    
    **URL de réponse** : `https://authentication.logmeininc.com/saml/acs`
    
    **RelayState** :
    
    - Pour l’application GoToMeeting, utilisez `https://global.gotomeeting.com`
    
    - Pour GoToTraining, utilisez `https://global.gototraining.com`
    
    - Pour GoToWebinar, utilisez `https://global.gotowebinar.com` 
    
    - Pour GoToAssist, utilisez `https://app.gotoassist.com`
    
5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. Dans une fenêtre différente du navigateur, connectez-vous à votre [Centre d’organisation GoToMeeting](https://organization.logmeininc.com/). Vous êtes invité à confirmer que le fournisseur d’identité a été mis à jour

7. Activez la case à cocher « Mon fournisseur d’identité a été mis à jour avec le nouveau domaine ». Lorsque vous avez terminé, cliquez sur **Terminé**.


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-gotomeeting-test-user"></a>Créer un utilisateur de test GoToMeeting

Dans cette section, un utilisateur appelé Britta Simon est créé dans GoToMeeting. GoToMeeting prend en charge le provisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans GoToMeeting, un nouvel utilisateur est créé lorsque vous tentez d’accéder à GoToMeeting.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support GoToMeeting](https://support.logmeininc.com/gotomeeting).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à GoToMeeting.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à GoToMeeting, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **GoToMeeting**.

    ![Lien GoToMeeting dans la liste des applications](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette GoToMeeting dans le volet d’accès, vous êtes connecté automatiquement à votre application GoToMeeting.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement de l’utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

