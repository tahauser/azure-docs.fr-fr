---
title: "Didacticiel : Intégration d’Azure Active Directory à Perception United States (Non-UltiPro) | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Perception United States (Non-UltiPro)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: d94d233a12e51bf851a791fda481b91c513d64b7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Didacticiel : Intégration d’Azure Active Directory à Perception United States (Non-UltiPro)

Dans ce didacticiel, vous allez apprendre à intégrer Perception United States (Non-UltiPro) à Azure Active Directory (Azure AD).

L’intégration de Perception United States (Non-UltiPro) à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Perception United States (Non-UltiPro).
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Perception United States (Non-UltiPro) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Perception United States (Non-UltiPro), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Perception United States (Non-UltiPro) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Perception United States (Non-UltiPro) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Ajout de Perception United States (Non-UltiPro) à partir de la galerie
Pour configurer l’intégration de Perception United States (Non-UltiPro) à Azure AD, vous devez ajouter Perception United States (Non-UltiPro) à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Perception United States (Non-UltiPro) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Perception United States (Non-UltiPro)**, sélectionnez **Perception United States (Non-UltiPro)** à partir du volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Perception United States (Non-UltiPro) dans la liste des résultats](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Perception United States (Non-UltiPro) grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur équivalent à l’utilisateur Azure AD dans Perception United States (Non-UltiPro). En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Perception United States (Non-UltiPro) associé doit être établie.

Dans Perception United States (Non-UltiPro), affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Perception United States (Non-UltiPro), vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Perception United States (Non-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)** pour avoir dans Perception United States (Non-UltiPro) un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Perception United States (Non-UltiPro).

**Pour configurer l’intégration d’Azure AD avec Perception United States (Non-UltiPro), procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Perception United States (Non-UltiPro)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. Dans la section **Domaine et URL Perception United States (Non-UltiPro)**, procédez comme suit :

    ![Informations d’authentification Domaine et URL Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. Dans la zone de texte **Identificateur**, saisissez l’URL : `https://perception.kanjoya.com/sp`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Vous mettrez à jour la valeur avec l’URL de réponse réelle. La procédure est expliquée plus loin dans le didacticiel.
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Perception United States (Non-UltiPro)**, cliquez sur **Configurer Perception United States (Non-UltiPro)** pour ouvrir la fenêtre **Configurer l’authentification unique**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    a. L’application **Perception United States (Non-UltiPro)** requiert le codage URI de la valeur d’**ID d’entité SAML** que vous avez copiée. Pour obtenir la valeur encodée en URI, utilisez le lien suivant : **http://www.url-encode-decode.com/**.

    b. Après l’obtention de la valeur encodée en URI, associez-la à l’**URL de réponse** comme indiqué ci-dessous :

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Collez la valeur ci-dessus dans la zone de texte **URL de réponse** dans la section **Domaine et URL Perception United States (Non-UltiPro)**.

    ![Configuration de Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Perception United States (Non-UltiPro) en tant qu’administrateur.

8. Dans la barre d’outils principale, cliquez sur **Paramètres du compte**.

    ![Utilisateur Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. Sur la page **Paramètres du compte** , procédez comme suit :

    ![Utilisateur Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Dans la zone de texte **Nom de l’entreprise**, entrez le nom de l’**entreprise**.
    
    b. Dans la zone de texte **Nom du compte**, entrez le nom du **compte**.

    c. Dans la zone de texte **Default Reply-To Email** (Adresse électronique de réponse par défaut), entrez une **adresse e-mail** valide.

    d. Sélectionnez le **fournisseur d’identité d’authentification unique** **SAML 2.0**.

10. Dans la page **Configuration SAML**, procédez comme suit :

    ![Configuration de l’authentification unique Perception United States (Non-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Sélectionnez le **type de NameID SAML** **E-MAIL**.

    b. Dans la zone de texte **Nom de configuration d’authentification unique**, tapez le nom de votre **configuration**.
    
    c. Dans la zone de texte **Nom du fournisseur d’identité**, collez la valeur de l’**ID d’entité SAML** copié à partir du portail Azure. 

    d. Dans la zone de texte **Domaine SAML**, entrez le domaine comme **@contoso.com**.

    e. Cliquez sur **Télécharger à nouveau** pour télécharger le fichier de **XML de métadonnées**.

    f. Cliquez sur **Update**.


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Créer un utilisateur de test Perception United States (Non-UltiPro)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Perception United States (Non-UltiPro). Collaborez avec l’[équipe de support Perception United States (Non-UltiPro)](http://www.ultimatesoftware.com/Contact/ContactUs) pour ajouter les utilisateurs dans la plateforme Perception United States (Non-UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Perception United States (Non-UltiPro).

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Perception United States (Non-UltiPro), procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Perception United States (Non-UltiPro)**.

    ![Lien Perception United States (Non-UltiPro) dans la liste des applications](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Perception United States (Non-UltiPro) dans le volet d’accès, vous devez être connecté automatiquement à votre application Perception United States (Non-UltiPro).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_203.png

