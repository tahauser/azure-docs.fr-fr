---
title: "Tutorial: Intégration d'Azure Active Directory à Moxtra | Microsoft Docs"
description: "Découvrez comment configurer l'authentification unique entre Azure Active Directory et Moxtra."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 3b10d954a7759fe945b162119d760fd6ad44317c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Didacticiel : Intégration d'Azure Active Directory avec Moxtra

Dans ce didacticiel, vous allez apprendre à intégrer Moxtra à Azure Active Directory (Azure AD).

L’intégration de Moxtra dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Moxtra
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Moxtra (via l'authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l'intégration d'Azure AD avec Moxtra, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Moxtra pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Moxtra à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-moxtra-from-the-gallery"></a>Ajout de Moxtra à partir de la galerie
Pour configurer l'intégration de Moxtra à Azure AD, vous devez ajouter Moxtra à votre liste d'applications SaaS gérées à partir de la galerie.

**Pour ajouter Moxtra à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Moxtra**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. Dans le panneau des résultats, sélectionnez **Moxtra**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Moxtra, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Moxtra correspondant dans Azure AD. En d'autres termes, il faut établir une relation entre l'utilisateur Azure AD et l'utilisateur Moxtra associé.

Dans Moxtra, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l'authentification unique Azure AD avec Moxtra, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Moxtra](#creating-a-moxtra-test-user)** pour obtenir un équivalent de Britta Simon dans Moxtra lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Moxtra.

**Pour configurer l'authentification unique Azure AD avec Moxtra, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Moxtra**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. Dans la section **Domaine et URL Moxtra**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://www.moxtra.com/service/#login`

4. L’application Moxtra attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple de cette configuration. 

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < ID d’entité SAML > 

    > [!Note]
    > La valeur de l’attribut **idpid** n’est pas réelle. Pour obtenir la valeur réelle, reportez-vous à la section **Référence rapide**, dans la rubrique **Configuration de Moxtra**.
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Cliquez sur **OK**.
    
5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de Moxtra**, cliquez sur **Configurer Moxtra** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. Dans une autre fenêtre de navigateur, connectez-vous à votre site d'entreprise Moxtra en tant qu'administrateur.

9. Dans la barre d’outils située à gauche, cliquez sur **Console Administrateur > Authentification unique SAML**, puis sur **Nouveau**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. Sur la page **SAML** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Dans la zone de texte **Nom**, saisissez le nom de votre configuration (par ex., *SAML*). 
  
    b. Dans la zone de texte **ID d’entité IdP**, collez la valeur **ID d’entité SAML** que vous avez copiée à partir du portail Azure. 
 
    c. Dans la zone de texte **URL de connexion**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure. 
 
    d. Dans la zone de texte **AuthnContextClassRef**, tapez **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Dans la zone de texte **Format NameID**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Dans le bloc-notes, ouvrez le certificat que vous avez téléchargé depuis le portail Azure, copiez son contenu, puis collez-le dans la zone de texte **Certificat**.    
 
    g. Dans la zone de texte du domaine de messagerie SAML, tapez votre domaine de messagerie SAML.    
  
    >[!NOTE]
    >Pour connaître les étapes de vérification du domaine, cliquez sur le «**i**» ci-dessous.

    h. Cliquez sur **Update**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-moxtra-test-user"></a>Création d'un utilisateur de test Moxtra

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Moxtra.

**Pour créer un utilisateur appelé Britta Simon dans Moxtra, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Moxtra en tant qu’administrateur.

2. Dans la barre d’outils située à gauche, cliquez sur **Console Administrateur > Gestion des utilisateurs**, puis sur **Ajouter un utilisateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. Dans la boîte de dialogue **Ajouter un utilisateur** , procédez comme suit :
  
    a. Dans la zone de texte **First Name**, tapez **Britta**.
  
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
  
    c. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de Britta, telle qu’elle est indiquée sur le portail Azure.
  
    d. Dans la zone de texte **Division**, saisissez **Développement**.
  
    e. Dans la zone de texte **Service**, saisissez **Informatique**.
  
    f. Sélectionnez **Administrateur**.
  
    g. Cliquez sur **Ajouter**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Moxtra.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Moxtra, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Moxtra**.

    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Moxtra dans le volet d'accès, vous êtes connecté automatiquement à votre application Moxtra.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

