---
title: "Didacticiel : Intégration d’Azure Active Directory à Dome9 Arc | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dome9 Arc."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 2ce4bb1be8b0124c69991765e18ce9922bd2f4a4
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Didacticiel : Intégration d’Azure Active Directory à Dome9 Arc

Dans ce didacticiel, vous allez apprendre à intégrer Dome9 Arc à Azure Active Directory (Azure AD).

L’intégration de Dome9 Arc à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Dome9 Arc.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Dome9 Arc (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Dome9 Arc, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Dome9 Arc pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Dome9 Arc à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-dome9-arc-from-the-gallery"></a>Ajout de Dome9 Arc à partir de la galerie
Pour configurer l’intégration de Dome9 Arc à Azure AD, vous devez ajouter Dome9 Arc depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Dome9 Arc à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Dome9 Arc**, sélectionnez **Dome9 Arc** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Dome9 Arc dans la liste des résultats](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Dome9 Arc avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Dome9 Arc équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Dome9 Arc associé doit être établie.

Dans Dome9 Arc, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Dome9 Arc, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Dome9 Arc](#create-a-dome9-arc-test-user)** pour avoir un équivalent de Britta Simon dans Dome9 Arc lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Dome9 Arc.

**Pour configurer l’authentification unique Azure AD avec Dome9 Arc, effectuez les étapes suivantes :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Dome9 Arc**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Dans la section **Domaines et URL Dome9 Arc**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Dome9 Arc Domain and URLs (Domaine et URL Dome9 Arc)](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. Dans la zone de texte **Identificateur**, tapez l’URL : `https://secure.dome9.com/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Vous allez sélectionner le nom de votre société dans le portail d’administration dome9, comme expliqué plus loin dans le didacticiel.

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Dome9 Arc Domain and URLs (Domaine et URL Dome9 Arc)](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe de support technique de Dome9 Arc](https://dome9.com/about/contact-us/). 

5. Votre application Dome9 Arc attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configurer l’authentification unique attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut  | Valeur de l’attribut | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles | 
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique add attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Configurer l’authentification unique edit attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. Dans la section **Configuration de Dome9 Arc**, cliquez sur **Configurer Dome9 Arc** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Dome9 Arc en tant qu’administrateur.

11. Cliquez sur les **paramètres du profil** dans l’angle supérieur droit puis cliquez sur les **paramètres du compte**. 

    ![Configuration de Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Accédez à **SSO**, puis cliquez sur **ACTIVER**.

    ![Configuration de Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. Dans la section de la configuration de l’authentification unique, effectuez les étapes suivantes :

    ![Configuration de Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Entrez le nom de la société dans la zone de texte **Account ID** (ID de compte). Cette valeur sera utilisée dans l’URL de réponse mentionnée dans la section de l’URL du portail Azure.

    b. Dans la zone de texte **Issuer** (Émetteur), collez la valeur de **ID d’entité SAML** que vous avez copiée depuis le portail Azure.

    c. Dans la zone de texte **Idp endpoint url** (URL du point de terminaison IdP), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée depuis le portail Azure.

    d. Ouvrez le certificat codé en base64 que vous avez téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 certificate** (Certificat X.509).

    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-dome9-arc-test-user"></a>Créer un utilisateur de test Dome9 Arc

Pour permettre aux utilisateurs Azure AD de se connecter à Dome9 Arc, vous devez les provisionner dans Dome9 Arc. Dome9 Arc prend en charge le provisionnement juste-à-temps, mais pour que cela fonctionne correctement, l’utilisateur doit sélectionner un **rôle** particulier et se l’assigner.

   >[!Note] 
   >Pour la création de **rôle** et autres détails, contactez [l’équipe de support client de Dome9 Arc](https://dome9.com/about/contact-us/).

**Pour provisionner un compte d’utilisateur manuellement, effectuez les étapes suivantes :**

1. Connectez-vous au site d’entreprise Dome9 Arc en tant qu’administrateur.

2. Cliquez sur **Users & Roles** (Utilisateurs et rôles), puis cliquez sur **Users** (utilisateurs).

    ![Ajouter un employé](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Cliquez sur**ADD USER** (Ajouter un utilisateur).

    ![Ajouter un employé](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. Dans la section **Create User** , procédez comme suit :
    
    ![Ajouter un employé](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple, Brittasimon@contoso.com.

    b. Dans la zone de texte **First Name**, entrez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Last Name**, entrez le nom de l’utilisateur, par exemple Simon.

    d. Définissez **SSO User** (SSO utilisateur) sur **On** (Activé).

    e. Cliquez sur **CREATE** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dome9 Arc.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Dome9 Arc, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Dome9 Arc**.

    ![Lien Dome9 Arc dans la liste des applications](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Dome9 Arc dans le volet d’accès, vous devez être connecté automatiquement à votre application Dome9 Arc.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

