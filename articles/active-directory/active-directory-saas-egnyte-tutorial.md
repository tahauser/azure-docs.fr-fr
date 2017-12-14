---
title: "Didacticiel : Intégration d’Azure Active Directory avec Egnyte | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Egnyte."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 94c71859aff01b60cee1b49664ad62257f9f9f2a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Didacticiel : Intégration d’Azure Active Directory à Egnyte

Dans ce didacticiel, vous allez apprendre à intégrer Egnyte à Azure Active Directory (Azure AD).

L’intégration d’Egnyte dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Egnyte.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Egnyte (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Egnyte, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Egnyte pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Egnyte à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-egnyte-from-the-gallery"></a>Ajout d’Egnyte à partir de la galerie
Pour configurer l’intégration d’Egnyte avec Azure AD, vous devez ajouter Egnyte à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Egnyte à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Egnyte**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. Dans le volet de résultats, sélectionnez **Egnyte**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Egnyte, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Egnyte correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Egnyte associé doit être établie.

Dans Egnyte, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Egnyte, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Egnyte](#creating-an-egnyte-test-user)** pour obtenir un équivalent de Britta Simon dans Egnyte lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Egnyte.

**Pour configurer l’authentification unique Azure AD avec Egnyte, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Egnyte**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Dans la section **Domaine et URL Egnyte**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Contactez [l’équipe de support technique Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) pour obtenir cette valeur. 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Egnyte**, cliquez sur **Configurer Egnyte** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Egnyte en tant qu’administrateur.

8. Cliquez sur **Settings**.
   
   ![Paramètres](./media/active-directory-saas-egnyte-tutorial/ic787819.png "Paramètres")

9. Dans le menu, cliquez sur **Settings**.

   ![Paramètres](./media/active-directory-saas-egnyte-tutorial/ic787820.png "Paramètres")

10. Cliquez sur l’onglet **Configuration** puis sur **Security**.

    ![Sécurité](./media/active-directory-saas-egnyte-tutorial/ic787821.png "Sécurité")

11. Dans la section **Single Sign-On Authentication** , procédez comme suit :

    ![Single Sign On Authentication](./media/active-directory-saas-egnyte-tutorial/ic787822.png "Single Sign On Authentication")   
    
    a. Pour **Single sign-on authentication**, sélectionnez **SAML 2.0**.
   
    b. Pour **Identity provider**, sélectionnez **AzureAD**.
   
    c. Collez **l’URL du service d’authentification unique SAML** copiée à partir du portail Azure dans la zone de texte **URL de connexion du fournisseur d’identité**.
   
    d. Dans la zone de texte **ID d’entité du fournisseur d’identité**, collez l’**ID d’entité SAML** que vous avez copié à partir du portail Azure.
      
    e. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de fournisseur d’identité**.
   
    f. Pour **Default user mapping**, sélectionnez **Email address**.
   
    g. Pour **Use domain-specific issuer value**, sélectionnez **disabled**.
   
    h. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-egnyte-test-user"></a>Création d’un utilisateur de test Egnyte

Pour se connecter à Egnyte, les utilisateurs d’Azure AD doivent être approvisionnés dans Egnyte. Dans le cas de Egnyte, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Egnyte** en tant qu’administrateur.

2. Accédez à **Settings \> Users & Groups**.

3. Cliquez sur **Add New User**, puis sélectionnez le type d’utilisateur à ajouter.
   
   ![Utilisateurs](./media/active-directory-saas-egnyte-tutorial/ic787824.png "Utilisateurs")

4. Dans la section **New Standard User** , procédez comme suit :
   
   ![New Standard User](./media/active-directory-saas-egnyte-tutorial/ic787825.png "New Standard User")   

   a. Tapez **l’adresse de messagerie**, le **nom d’utilisateur** et les autres informations d’un compte Azure Active Directory valide à approvisionner.
   
   b. Cliquez sur **Save**.
    
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory recevra une notification par courrier électronique.
    >

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Egnyte fourni par ce site pour approvisionner des comptes d’utilisateurs AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Egnyte.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Egnyte, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Egnyte**.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette Egnyte dans le volet d’accès, vous devez vous connecter automatiquement à votre application Egnyte.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png

