---
title: "Didacticiel : Intégration d’Azure Active Directory à Boomi | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Boomi."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: f63f317cc213519c50b0f07b4f93842a72500a82
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Didacticiel : Intégration d’Azure Active Directory à Boomi

Dans ce didacticiel, vous allez apprendre à intégrer Boomi à Azure Active Directory (Azure AD).

L’intégration de Boomi dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Boomi
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Boomi (par authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Boomi, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Boomi pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Boomi depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-boomi-from-the-gallery"></a>Ajout de Boomi depuis la galerie
Pour configurer l’intégration de Boomi avec Azure AD, vous devez ajouter Boomi disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Boomi à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Boomi**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_search.png)

5. Dans le volet de résultats, sélectionnez **Boomi**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Boomi avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Boomi équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Boomi associé doit être établie.

Dans Boomi, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Boomi, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Boomi](#creating-a-boomi-test-user)** pour avoir un équivalent de Britta Simon dans Boomi lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Boomi.

**Pour configurer l’authentification unique Azure AD avec Boomi, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Boomi**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. Dans la section **Domaine et URL Boomi**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://platform.boomi.com/sso/<accountname>/saml`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://platform.boomi.com/sso/<accountname>/saml`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Contactez l’[équipe de support technique de Boomi](https://boomi.com/company/contact/) pour obtenir ces valeurs.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png)

4. L’application Boomi attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, procédez comme suit pour chaque ligne indiquée dans le tableau ci-dessous :

    | Nom de l'attribut | Valeur de l’attribut |
    | -------------- | --------------- |
    | FEDERATION_ID | user.mail |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_04.png)
    
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

7. Dans la section **Boomi Configuration** (Configuration de Boomi), cliquez sur **Configure Boomi** (Configurer Boomi) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur. 

9. Accédez à **Nom de la société** et allez dans **Configurer**.

10. Cliquez sur l’onglet **Options SSO** et suivez les étapes ci-dessous.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Cochez la case **Enable SAML Single Sign-On**.

    b. Cliquez sur **Importer** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat du fournisseur d’identité**.
    
    c. Dans la zone de texte **URL de connexion du fournisseur identité**, copiez la valeur de **l’URL du service d’authentification unique SAML** indiquée dans la fenêtre de configuration de l’application Azure AD.

    d. Dans **Federation Id Location** (Emplacement ID de fédération), sélectionnez le bouton radio **Federation Id is in FEDERATION_ID Attribute element** (L’ID de fédération se trouve dans l’élément d’attribut FEDERATION_ID). 

    e. Cliquez sur le bouton **Enregistrer** .

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-boomi-test-user"></a>Création d’un utilisateur de test Boomi

Pour permettre aux utilisateurs Azure AD de se connecter à Boomi, vous devez les approvisionner dans Boomi. En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

2. Après connexion, accédez à **Gestion des utilisateurs** et allez dans **Utilisateurs**.

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Utilisateurs")

3. Cliquez sur l’icône **+**, et la boîte de dialogue **Ajouter/gérer les rôles utilisateur** s’ouvre.

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Utilisateurs")

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Utilisateurs")

    a. Dans la zone de texte **Adresse e-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple BrittaSimon@contoso.com.
    
    b. Dans la zone de texte **Prénom**, tapez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Nom**, tapez le nom de l’utilisateur, par exemple Simon.
    
    d. Entrez **l’ID de fédération** de l’utilisateur. Chaque utilisateur doit posséder un ID de fédération qui l’identifie de façon unique dans le compte.
    
    e. Affectez le rôle **Utilisateur standard** à l’utilisateur. Ne lui affectez pas le rôle d’administrateur, car cela lui donnerait l’accès normal à AtomSphere, ainsi que l’accès en authentification unique.
    
    f. Cliquez sur **OK**.
    
    > [!NOTE]
    > L’utilisateur ne recevra pas d’e-mail de notification de bienvenue contenant un mot de passe pouvant être utilisé pour se connecter au compte AtomSphere, car son mot de passe est géré par le biais du fournisseur d’identité. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Boomi pour approvisionner des comptes d’utilisateur Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Boomi.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Boomi, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Boomi**.

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Boomi dans le volet d’accès, vous devez être connecté automatiquement à votre application Boomi.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png

