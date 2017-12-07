---
title: "Didacticiel : Intégration d’Azure Active Directory avec FileCloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et FileCloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2263e583-3eb2-4a06-982d-33f5f54858f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: jeedes
ms.openlocfilehash: f1f16865a3adb55fc63c09c1dbdc9720b02f9c33
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Didacticiel : Intégration d’Azure Active Directory avec FileCloud

Dans ce didacticiel, vous allez apprendre à intégrer FileCloud à Azure Active Directory (Azure AD).

L’intégration de FileCloud avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à FileCloud.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FileCloud (authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec FileCloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FileCloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FileCloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-filecloud-from-the-gallery"></a>Ajout de FileCloud à partir de la galerie
Pour configurer l’intégration de FileCloud à Azure AD, vous devez ajouter FileCloud à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter FileCloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **FileCloud**, sélectionnez **FileCloud** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![FileCloud dans la liste des résultats](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec FileCloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel est l’utilisateur FileCloud équivalent à un utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur associé dans FileCloud.

Dans FileCloud, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec FileCloud, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test FileCloud](#create-a-filecloud-test-user)** pour avoir un équivalent de Britta Simon dans FileCloud, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application FileCloud.

**Pour configurer l’authentification unique Azure AD avec FileCloud, procédez comme suit :**

1. Sur le portail Azure, à la page d’intégration de l’application **FileCloud**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_samlbase.png)

3. Dans la section **Domaine et URL FileCloud**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL FileCloud](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.filecloudonline.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de prise en charge des clients FileCloud](mailto:support@codelathe.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de Configurer l’authentification unique](./media/active-directory-saas-filecloud-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de FileCloud**, cliquez sur **Configurer FileCloud** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de FileCloud](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre client FileCloud en tant qu’administrateur.

8. Dans le volet de navigation gauche, cliquez sur **Paramètres**. 
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

9. Dans la section Paramètres, cliquez sur l’onglet **SSO**. 
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

10. Dans le panneau **Single Sign On (SSO) Settings** (Paramètres d’authentification unique), sélectionnez **SAML** comme **Default SSO Type** (Type d’authentification unique par défaut).
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

11. Dans la zone de texte **URL du point de terminaison IdP**, collez la valeur **ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

12. Ouvrez votre fichier de métadonnées téléchargé dans le bloc-notes, copiez le contenu de celui-ci dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Meta Data** (métadonnées IdP) du panneau **SAML Settings** (Paramètres SAML).

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

13. Cliquez sur le bouton **Enregistrer** .

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-filecloud-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-filecloud-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-filecloud-test-user"></a>Créer un utilisateur de test de FileCloud

L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans FileCloud. FileCloud prend en charge le déploiement juste-à-temps, qui est l’option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à FileCloud s’il n’existe pas encore.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de prise en charge des clients FileCloud](mailto:support@codelathe.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FileCloud.

![Assigner le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à FileCloud, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **FileCloud**.

    ![Lien FileCloud dans la liste Applications](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette FileCloud dans le volet d’accès, vous devez être automatiquement authentifié auprès de votre application FileCloud.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png

