---
title: "Didacticiel : Intégration d’Azure Active Directory à ScreenSteps | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et ScreenSteps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: ee2adf151d47931ab6a2e3bdc5e20c16deb8e8e4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Didacticiel : Intégration d’Azure AD à ScreenSteps

Dans ce didacticiel, vous allez apprendre à intégrer ScreenSteps à Azure Active Directory (Azure AD).

L’intégration de ScreenSteps à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ScreenSteps.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ScreenSteps (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à ScreenSteps, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ScreenSteps pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ScreenSteps à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-screensteps-from-the-gallery"></a>Ajout de ScreenSteps à partir de la galerie
Pour configurer l’intégration de ScreenSteps à Azure AD, vous devez ajouter ScreenSteps à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter ScreenSteps à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **ScreenSteps**, sélectionnez **ScreenSteps** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![ScreenSteps dans la liste des résultats](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ScreenSteps avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ScreenSteps équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur ScreenSteps associé.

Dans ScreenSteps, attribuez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ScreenSteps, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test ScreenSteps](#create-a-screensteps-test-user)** pour avoir un équivalent de Britta Simon dans ScreenSteps lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ScreenSteps.

**Pour configurer l’authentification unique Azure AD avec ScreenSteps, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **ScreenSteps**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. Dans la section **Domaine et URL ScreenSteps**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Vous devez remplacer cette valeur par l’URL de connexion réelle. La procédure est expliquée plus loin dans le didacticiel. 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de ScreenSteps**, cliquez sur **Configurer ScreenSteps** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise ScreenSteps en tant qu’administrateur.

8. Cliquez sur **Account Settings** (Paramètres du compte).

    ![Account management](./media/active-directory-saas-screensteps-tutorial/ic778523.png "Account management")

9. Cliquez sur **Single Sign-on**.

    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/ic778524.png "Remote authentication")

10. Cliquez sur **Créer un point de terminaison d’authentification unique**.

    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/ic778525.png "Remote authentication")

11. Dans la section **Créer un point de terminaison d’authentification unique**, procédez comme suit :

    ![Create an authentication endpoint](./media/active-directory-saas-screensteps-tutorial/ic778526.png "Create an authentication endpoint")
    
    a. Dans la zone de texte **Title** , tapez un titre.
    
    b. Dans la liste **Mode**, sélectionnez **SAML**.
    
    c. Cliquez sur **Créer**.

12. Cliquez sur **Edit** (Modifier) pour modifier le nouveau point de terminaison.

    ![Modifier un point de terminaison](./media/active-directory-saas-screensteps-tutorial/ic778528.png "Modifier un point de terminaison")

13. Dans la section **Edit Single Sign-on Endpoint (Modifier un point de terminaison d’authentification unique)**, procédez comme suit :

    ![Remote authentication endpoint](./media/active-directory-saas-screensteps-tutorial/ic778527.png "Remote authentication endpoint")

    a. Cliquez sur **Upload new SAML Certificate file** (Charger le nouveau fichier de certificat SAML), puis chargez le certificat que vous avez téléchargé à partir du portail Azure.
    
    b. Collez la valeur **URL du service d’authentification unique SAML** copiée dans le portail Azure dans la zone de texte **Remote Login URL** (URL de connexion distante).
    
    c. Collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure dans la zone de texte **Log out URL** (URL de déconnexion).
    
    d. Sélectionnez un **groupe** auquel attribuer des utilisateurs lorsque ces derniers sont approvisionnés.
    
    e. Cliquez sur **Update**.

    f. Copiez la valeur **SAML Consumer URL** (URL du consommateur SAML) dans le Presse-papiers, puis collez-la dans la zone de texte **URL de connexion** située dans la section **Domaine et URL ScreenSteps**.
    
    g. Revenez à **Edit Single Sign-on Endpoint (Modifier un point de terminaison d’authentification unique)**.
    
    h. Cliquez sur le bouton **Make default for account** (Configurer par défaut pour le compte) pour utiliser ce point de terminaison pour tous les utilisateurs qui se connectent à ScreenSteps. Vous pouvez également cliquer sur le bouton **Ajouter au site** pour utiliser ce point de terminaison pour des sites spécifiques dans **ScreenSteps**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-screensteps-test-user"></a>Créer un utilisateur de test ScreenSteps

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans ScreenSteps. Pour ajouter des utilisateurs à la plateforme ScreenSteps, contactez [l’équipe du support ScreenSteps](https://www.screensteps.com/contact). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ScreenSteps.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à ScreenSteps, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ScreenSteps**.

    ![Lien ScreenSteps dans la liste des applications](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette ScreenSteps dans le volet d’accès, vous devez être automatiquement connecté à votre application ScreenSteps.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

