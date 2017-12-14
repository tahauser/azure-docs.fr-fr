---
title: "Didacticiel : Intégration d’Azure Active Directory à Mimecast Admin Console | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mimecast Admin Console."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: 64bb18caa046f8a08be8f229e4c82a34306a8f72
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Didacticiel : Intégration d’Azure Active Directory à Mimecast Admin Console

Dans ce didacticiel, vous allez apprendre à intégrer Mimecast Admin Console à Azure Active Directory (Azure AD).

L’intégration de Mimecast Admin Console à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Mimecast Admin Console.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Mimecast Admin Console (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Mimecast Admin Console, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Mimecast Admin Console pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Mimecast Admin Console à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Ajout de Mimecast Admin Console à partir de la galerie
Pour configurer l’intégration de Mimecast Admin Console à Azure AD, vous devez ajouter Mimecast Admin Console à partir de la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Mimecast Admin Console à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Mimecast Admin Console**, sélectionnez **Mimecast Admin Console** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Mimecast Admin Console dans la liste des résultats](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mimecast Admin Console sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Mimecast Admin Console équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Mimecast Admin Console associé.

Dans Mimecast Admin Console, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Mimecast Admin Console, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Mimecast Admin Console](#create-a-mimecast-admin-console-test-user)** pour obtenir un équivalent de Britta Simon dans Mimecast Admin Console lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Mimecast Admin Console.

**Pour configurer l’authentification unique Azure AD avec Mimecast Admin Console, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Mimecast Admin Console**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. Dans la section **Domaine et URL Mimecast Admin Console**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL :
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > L’URL d’ouverture de session est spécifique à la région.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Mimecast Admin Console**, cliquez sur **Configurer Mimecast Admin Console** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Admin Console en tant qu’administrateur.

8. Accédez à **Services \> Application**.

    ![Services](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "Services")

9. Cliquez sur **Authentication Profiles**.

    ![Profils d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "Profils d’authentification")
    
10. Cliquez sur **New Authentication Profile**.

    ![Nouveaux profils d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "Nouveaux profils d’authentification")

11. Dans la section **Authentication Profile** , procédez comme suit :

    ![Profil d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "Profil d’authentification")
    
    a. Dans la zone de texte **Description** , indiquez un nom pour votre configuration.
    
    b. Sélectionnez **Enforce SAML Authentication for Mimecast Admin Console**.
    
    c. Pour **Provider**, sélectionnez **Azure Active Directory**.
    
    d. Collez **l’ID d’entité SAML** que vous avez copié sur le portail Azure dans la zone de texte **Issuer URL** (URL de l’émetteur).
    
    e. Collez **l’URL du service d’authentification unique SAML**, copiée à partir du portail Azure, dans la zone de texte **Login URL** (URL de connexion).

    f. Collez **l’URL du service d’authentification unique SAML**, copiée à partir du portail Azure, dans la zone de texte **Logout URL** (URL de déconnexion).
    
    >[!NOTE]
    >Les valeurs d’URL de connexion et de déconnexion sont identiques pour Mimecast Admin Console.
    
    g. Dans le Bloc-notes, ouvrez le certificat codé en base 64 que vous avez téléchargé à partir du portail Azure, supprimez la première ligne (« *--* ») et la dernière ligne (« *--* »), copiez le contenu restant dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)** (Certificat du fournisseur d’identité (Métadonnées)).
    
    h. Sélectionnez **Allow Single Sign On**.
    
    i. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Créer un utilisateur de test Mimecast Admin Console

Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Admin Console, vous devez les approvisionner dans Mimecast Admin Console. Dans le cas de Mimecast Admin Console, l’approvisionnement est une tâche manuelle.

* Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à **Mimecast Admin Console** en tant qu’administrateur.
2. Accédez à **Directories \> Internal**.
   
   ![Répertoires](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "Répertoires")
3. Cliquez sur **Register New Domain**.
   
   ![Enregistrer un nouveau domaine](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "Enregistrer un nouveau domaine")
4. Après avoir créé votre domaine, cliquez sur **New Address**.
   
   ![Nouvelle adresse](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "Nouvelle adresse")
5. Dans la boîte de dialogue New Address, procédez comme suit :
   
   ![Enregistrer](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "enregistrer")
   
   a. Tapez l’adresse e-mail, le nom global, le mot de passe et sa confirmation pour un compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address**, **Global Name**, **Password** et **Confirm Password**.

   b. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fournis par Mimecast Admin Console, pour approvisionner des comptes d’utilisateur Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mimecast Admin Console.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à Mimecast Admin Console, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Mimecast Admin Console**.

    ![Lien Mimecast Admin Console dans la liste des applications](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Mimecast Admin Console dans le panneau d’accès, vous devez être connecté automatiquement à votre application Mimecast Admin Console.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png

