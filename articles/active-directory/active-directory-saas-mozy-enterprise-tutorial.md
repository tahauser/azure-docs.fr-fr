---
title: "Didacticiel : Intégration d’Azure Active Directory avec Mozy Enterprise | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mozy Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: 026cec89e5c3d26037d59b0a4ec1195d245c9643
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory avec Mozy Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer Mozy Enterprise avec Azure Active Directory (Azure AD).

L’intégration du logiciel Mozy Enterprise avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Mozy Enterprise.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Mozy Enterprise (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Mozy Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Mozy Enterprise pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Mozy Enterprise à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Ajout de Mozy Enterprise à partir de la galerie
Pour configurer l’intégration de Mozy Enterprise à Azure AD, vous devez ajouter Mozy Enterprise à votre liste d’applications SaaS gérées, depuis la galerie.

**Pour ajouter Mozy Enterprise à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Mozy Enterprise**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. Dans le panneau des résultats, sélectionnez **Mozy Enterprise**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mozy Enterprise avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Mozy Enterprise équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Mozy Enterprise associé doit être établie.

Dans Mozy Enterprise, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Mozy Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Mozy Enterprise](#creating-a-mozy-enterprise-test-user)** pour obtenir un équivalent de Britta Simon dans Mozy Enterprise, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Mozy Enterprise.

**Pour configurer l’authentification unique Azure AD avec Mozy Enterprise, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Mozy Enterprise**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. Dans la section **Domaine et URL Mozy Enterprise**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Contactez l’[équipe de support Mozy Enterprise](http://support.mozy.com/) pour obtenir cette valeur.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Mozy Enterprise**, cliquez sur **Configurer Mozy Enterprise** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mozy Enterprise en tant qu’administrateur.

8. Dans la section **Configuration**, cliquez sur **Authentication Policy**.
   
   ![Stratégie d’authentification](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "Stratégie d’authentification")

9. Dans la section **Authentication Policy** , procédez comme suit :
   
   ![Stratégie d’authentification](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "Stratégie d’authentification")
   
   a. Sélectionnez **Directory Service** comme **Provider**.
   
   b. Sélectionnez **Use LDAP Push**.
   
   c. Cliquez sur l’onglet **SAML Authentication** .
   
   d. Collez l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure dans la zone de texte de l’**URL d’authentification**.
   
   e. Collez l’**ID d’entité SAML** que vous avez copié sur le portail Azure dans la zone de texte du **Point de terminaison SAML**.
   
   f. Ouvrez votre certificat téléchargé, codé en base 64, dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat SAML**.
   
   g. Sélectionnez **Enable SSO for Admins to log in with their network credentials**.
   
   h. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Création d’un utilisateur de test Mozy Enterprise

Pour permettre aux utilisateurs Azure AD de se connecter à Mozy Enterprise, vous devez les approvisionner dans Mozy Enterprise. Dans le cas de Mozy Enterprise, l’approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Mozy Enterprise pour approvisionner des comptes d’utilisateur Azure Active Directory.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Mozy Enterprise** .

2. Cliquez sur **Users**, puis sur **Add New User**.
   
   ![Utilisateurs](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "Utilisateurs")
   
   >[!NOTE]
   >L’option **Add New User** ne s’affiche que si **Mozy** est sélectionné comme fournisseur sous **Authentication policy**. Si l’authentification SAML est configurée, les utilisateurs sont ajoutés automatiquement lors de la première connexion à l’aide de l’authentification unique.
    
3. Dans la boîte de dialogue New User, procédez comme suit :
   
   ![Ajouter des utilisateurs](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "ajouter des utilisateurs")
   
   a. Dans la liste **Choose a Group** , sélectionnez un groupe.
   
   b. Dans la liste **What type of user** , sélectionnez un type.
   
   c. Dans la zone de texte **Username** , tapez le nom de l’utilisateur Azure AD.
   
   d. Dans la zone de texte **Email** , tapez l’adresse de messagerie de l’utilisateur Azure AD.
   
   e. Sélectionnez **Send user instruction email**.
   
   f. Cliquez sur **Add User(s)**.

     >[!NOTE]
     > Après la création de l’utilisateur, un message électronique sera envoyé à l’utilisateur Azure AD avec un lien pour confirmer le compte avant qu’il ne soit activé.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mozy Enterprise.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Mozy Enterprise, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Mozy Enterprise**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Mozy Enterprise dans le panneau d’accès, la page de connexion de l’application Mozy Enterprise doit apparaître.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png

