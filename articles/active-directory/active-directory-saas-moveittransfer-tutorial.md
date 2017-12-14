---
title: "Didacticiel : Intégration d’Azure Active Directory à MOVEit Transfer - Azure AD integration | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et MOVEit Transfer - Azure AD integration."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 5d9955655119a51656d6b17ba1cecf624c416874
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Didacticiel : Intégration d’Azure Active Directory à MOVEit Transfer - Azure AD integration

Dans ce didacticiel, vous allez apprendre à intégrer MOVEit Transfer - Azure AD integration à Azure Active Directory (Azure AD).

L’intégration de MOVEit Transfer - Azure AD integration à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à MOVEit Transfer - Azure AD integration.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à MOVEit Transfer - Azure AD integration (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration de MOVEit Transfer - Azure AD integration à Azure AD, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement MOVEit Transfer - Azure AD integration pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MOVEit Transfer - Azure AD integration à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Ajout de MOVEit Transfer - Azure AD integration à partir de la galerie
Pour configurer l’intégration de MOVEit Transfer - Azure AD integration avec Azure AD, vous devez ajouter MOVEit Transfer - Azure AD integration, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter MOVEit Transfer - Azure AD integration à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **MOVEit Transfer - Azure AD integration**, sélectionnez **MOVEit Transfer - Azure AD integration** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![MOVEit Transfer - Azure AD integration dans la liste des résultats](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec MOVEit Transfer - Azure AD integration avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur MOVEit Transfer - Azure AD integration équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur MOVEit Transfer - Azure AD integration associé.

Dans MOVEit Transfer - Azure AD integration, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec MOVEit Transfer - Azure AD integration, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test MOVEit Transfer - Azure AD integration](#create-a-moveit-transfer---azure-ad-integration-test-user)** pour avoir un équivalent de Britta Simon dans MOVEit Transfer - Azure AD integration lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application MOVEit Transfer - Azure AD integration.

**Pour configurer l’authentification unique Azure AD avec MOVEit Transfer - Azure AD integration, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **MOVEit Transfer - Azure AD integration**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. Dans la section **Domaine et URL MOVEit Transfer - Azure AD integration**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://contoso.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://contoso.com/<tenatid>`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous pouvez vous reporter à ces valeurs ultérieurement dans la section **URL de métadonnées du fournisseur de service**, ou contacter l’[équipe de support technique MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) pour les obtenir.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Connectez-vous à votre client MOVEit Transfer - Azure AD integration en tant qu’administrateur.

7. Dans le volet de navigation gauche, cliquez sur **Paramètres**.

    ![Section Paramètres côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Cliquez sur le lien **Authentification unique** qui se trouve sous **Stratégies de sécurité -> Authentification des utilisateurs**.

    ![Stratégies de sécurité côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Cliquez sur le lien URL de métadonnées pour télécharger le document de métadonnées.

    ![URL de métadonnées du fournisseur de service](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Vérifiez que la valeur **entityID** correspond à celle de l’**identificateur** dans la section **Domaine et URL MOVEit Transfer - Azure AD integration**.
    * Vérifiez que l’URL d’emplacement **AssertionConsumerService** correspond à l’**URL de réponse** dans la section **Domaine et URL MOVEit Transfer - Azure AD integration**.
    
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité) pour ajouter un nouveau fournisseur d’identité fédéré.

    ![Ajouter un fournisseur d’identité](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Cliquez sur **Parcourir...** pour sélectionner le fichier de métadonnées que vous avez téléchargé à partir du portail Azure, puis cliquez sur **Ajouter un fournisseur d’identité** pour charger le fichier téléchargé.

    ![Fournisseur d’identité SAML](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Sélectionnez **Oui** sous **Activé** dans la page **Edit Federated Identity Provider Settings** (Modifier les paramètres du fournisseur d’identité fédéré...), puis cliquez sur **Enregistrer**.

    ![Paramètres de fournisseur d’identité fédérée](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. Dans la page **Edit Federated Identity Provider User Settings** (Modifier les paramètres utilisateur du fournisseur d’identité fédérée), effectuez les actions suivantes :
    
    ![Modifier les paramètres de fournisseur d’identité fédérée](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Sélectionnez **SAML NameID** comme **Nom de connexion**.
    
    b. Sélectionnez **Autre** comme **Nom complet** et dans la zone de texte **Nom de l’attribut**, insérez la valeur : `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Sélectionnez **Autre** comme **E-mail** et dans la zone de texte **Nom de l’attribut**, insérez la valeur : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Sélectionnez **Oui** sous **Auto-create account on signon** (Créer automatiquement un compte lors de l’authentification).
    
    e. Cliquez sur le bouton **Enregistrer** .

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Créer un utilisateur de test MOVEit Transfer - Azure AD integration

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration prend en charge l’approvisionnement juste-à-temps que vous avez activé. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à MOVEit Transfer - Azure AD integration s’il n’existe pas déjà.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MOVEit Transfer - Azure AD integration.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à MOVEit Transfer - Azure AD integration, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **MOVEit Transfer - Azure AD integration**.

    ![Lien MOVEit Transfer - Azure AD integration dans la liste des applications](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette MOVEit Transfer - Azure AD integration dans le volet d’accès, vous êtes connecté automatiquement à votre application MOVEit Transfer - Azure AD integration. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

