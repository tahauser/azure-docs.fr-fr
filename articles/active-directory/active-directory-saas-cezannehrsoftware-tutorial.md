---
title: "Didacticiel : Intégration d’Azure Active Directory avec Cezanne HR Software | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cezanne HR Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jeedes
ms.openlocfilehash: cf44d749ecbfcffb3d5a6e5e12aa49e66f7cde2e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Didacticiel : Intégration d’Azure Active Directory avec Cezanne HR Software

Dans ce didacticiel, vous allez apprendre à intégrer Cezanne HR Software à Azure Active Directory (Azure AD).

L’intégration de Cezanne HR Software dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Cezanne HR Software.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Cezanne HR Software (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Cezanne HR Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Cezanne HR Software pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Cezanne HR Software à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Ajout de Cezanne HR Software à partir de la galerie
Pour configurer l’intégration de Cezanne HR Software avec Azure AD, vous devez ajouter Cezanne HR Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cezanne HR Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Cezanne HR Software**, sélectionnez **Cezanne HR Software** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Cezanne HR Software dans la liste des résultats](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cezanne HR Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Cezanne HR Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Cezanne HR Software associé doit être établi.

Dans Cezanne HR Software, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Cezanne HR Software, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créez un utilisateur de test Cezanne HR Software](#create-a-cezannehrsoftware-test-user)** pour avoir un équivalent de Britta Simon dans Cezanne HR Software qui soit lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Cezanne HR Software.

**Pour configurer l’authentification unique Azure AD avec Cezanne HR Software, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Cezanne HR Software**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Dans la section **Domaine et URL Cezanne HR Software**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://w3.cezanneondemand.com/CezanneOnDemand/-/optyma`

    b. Dans la zone de texte **Identificateur**, tapez l’URL : `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Dans la zone de texte **URL de réponse**, tapez l’URL : `https://w3.cezanneondemand.com:443/cezanneondemand/-/optyma/Saml/samlp`

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

6. Dans **Configuration de Cezanne HR Software**, cliquez sur **Configurer Cezanne HR Software** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Cezanne HR Software en tant qu’administrateur.

8. Dans le volet de navigation gauche, cliquez sur **Configuration système**. Accédez aux **Paramètres de sécurité**. Accédez ensuite à **Configuration de l’authentification unique**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. Dans le panneau **Autoriser les utilisateurs à se connecter à l’aide de l’authentification unique**, cochez la case **SAML 2.0** et sélectionnez l’option **Configuration avancée**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Cliquez sur le bouton **Ajouter nouveau** .

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Dans la section **SAML 2.0 IDENTITY PROVIDERS** , procédez comme suit.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Entrez le nom de votre fournisseur d’identité en tant que **Nom d’affichage**.

    b. Dans la zone de texte **Identificateur d’entité**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure. 

    c. Modifiez la **Liaison SAML** sur POST.

    d. Dans la zone de texte **Point de terminaison de service du jeton de sécurité**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte User ID Attribute Name (Nom de l’attribut de l’identificateur d’utilisateur), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Cliquez sur **Télécharger** pour charger le certificat obtenu auprès du portail Azure.
    
    g. Cliquez sur le bouton **OK** . 

12. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Créer un utilisateur de test Cezanne HR Software

Pour permettre aux utilisateurs Azure AD de se connecter à Cezanne HR Software, vous devez les approvisionner dans Cezanne HR Software. Dans le cas de Cezanne HR Software, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1.  Connectez-vous à votre site d’entreprise Cezanne HR Software en tant qu’administrateur.

2.  Dans le volet de navigation gauche, cliquez sur **Configuration système**. Accédez à **Gérer les utilisateurs**. Accédez ensuite à **Ajouter un nouvel utilisateur**.

    ![Nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nouvel utilisateur")

3.  À la section **Détails de la personne** , procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nouvel utilisateur")
    
    a. Désactivez l’option **Utilisateur interne** .
    
    b. Dans la zone de texte **First Name** (Prénom), tapez le prénom de l’utilisateur, par exemple **Britta**.  
 
    c. Dans la zone de texte **Last Name** (Nom de famille), tapez le nom de l’utilisateur, par exemple **Simon**.
    
    d. Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

4.  À la section **Informations sur le compte** , procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nouvel utilisateur")
    
    a. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.
    
    b. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.
    
    c. Sélectionnez **Professionnel des RH** en tant que **Rôle de sécurité**.
    
    d. Cliquez sur **OK**.

5. Accédez à l’onglet **Authentification unique** et sélectionnez **Ajouter nouveau** dans la zone **Identificateurs SAML 2.0**.

    ![Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilisateur")

6. Choisissez votre **fournisseur d’identité** et dans la zone de texte **Identificateur d’utilisateur**, entrez l’adresse de messagerie du compte de Britta Simon.

    ![Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilisateur")
    
7. Cliquez sur le bouton **Enregistrer** .

    ![Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilisateur")

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cezanne HR Software.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à Cezanne HR Software, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Cezanne HR Software**.

    ![Lien Cezanne HR Software dans la liste des applications](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Cezanne HR Software dans le volet d’accès, vous devez être connecté automatiquement à votre application Cezanne HR Software.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

