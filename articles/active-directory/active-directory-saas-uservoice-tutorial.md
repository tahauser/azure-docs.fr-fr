---
title: "Didacticiel : Intégration d’Azure Active Directory à UserVoice | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et UserVoice."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 8ec3fd40b04dc6590b80c643a4429f97f7b874d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Didacticiel : Intégration d’Azure Active Directory à UserVoice

Dans ce didacticiel, vous allez apprendre à intégrer UserVoice à Azure Active Directory (Azure AD).

L’intégration d’UserVoice à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à UserVoice.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à UserVoice (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à UserVoice, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement UserVoice pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de UserVoice à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-uservoice-from-the-gallery"></a>Ajout de UserVoice à partir de la galerie
Pour configurer l’intégration de UserVoice avec Azure AD, vous devez ajouter UserVoice disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter UserVoice à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **UserVoice**, sélectionnez **UserVoice** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![UserVoice dans la liste des résultats](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec UserVoice, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur UserVoice correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur UserVoice associé doit être établie.

Dans UserVoice, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec UserVoice, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test UserVoice](#create-a-uservoice-test-user)** pour avoir un équivalent de Britta Simon dans UserVoice lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application UserVoice.

**Pour configurer l’authentification unique Azure AD avec UserVoice, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **UserVoice**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. Dans la section **Domaine et URL UserVoice**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.UserVoice.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique UserVoice](https://www.uservoice.com/).

4. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT** du certificat.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de UserVoice** , cliquez sur **Configurer UserVoice** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise UserVoice en tant qu’administrateur.

8. Dans la barre d’outils en haut, cliquez sur **Paramètres** et sélectionnez **Portail Web** dans le menu.
   
    ![Section Paramètres côté application](./media/active-directory-saas-uservoice-tutorial/ic777519.png "Paramètres")

9. Dans la section **Authentification utilisateur** de l’onglet **Portail Web**, cliquez sur **Modifier** pour ouvrir la page de la boîte de dialogue **Edit User Authentication** (Modifier l’authentification utilisateur).
   
    ![Onglet Portail Web](./media/active-directory-saas-uservoice-tutorial/ic777520.png "Portail Web")

10. Dans la page **Edit User Authentication** , procédez comme suit :
   
    ![Modifier l’authentification utilisateur](./media/active-directory-saas-uservoice-tutorial/ic777521.png "modifier l’authentification utilisateur")
   
    a. Cliquez sur **Single Sign-On (SSO)**.
 
    b. Collez la valeur de l’**URL du service d’authentification unique SAML** copiée dans le portail Azure dans la zone de texte **Connexion à distance SSO**.

    c. Collez la valeur de l’**URL de déconnexion** copiée dans le portail Azure dans la zone de texte **Déconnexion à distance SSO**.
 
    d. Collez la valeur **Empreinte** que vous avez copiée à partir du portail Azure dans la zone de texte **Empreinte SHA1 du certificat actuel**.
    
    e. Cliquez sur **Save authentication settings**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-uservoice-test-user"></a>Créer un utilisateur de test UserVoice

Pour permettre aux utilisateurs Azure AD de se connecter à UserVoice, ils doivent être approvisionnés dans UserVoice. Dans le cas d’UserVoice, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **UserVoice** .

2. Accédez à **Settings**.
   
    ![Paramètres](./media/active-directory-saas-uservoice-tutorial/ic777811.png "Paramètres")

3. Cliquez sur **General**.

4. Cliquez sur **Agents and permissions**.
   
    ![Agents et autorisations](./media/active-directory-saas-uservoice-tutorial/ic777812.png "Agents et autorisations")

5. Cliquez sur **Add admins**.
   
    ![Ajouter des administrateurs](./media/active-directory-saas-uservoice-tutorial/ic777813.png "ajouter des administrateurs")

6. Dans la boîte de dialogue **Invite admins** , procédez comme suit :
   
    ![Inviter des administrateurs](./media/active-directory-saas-uservoice-tutorial/ic777814.png "inviter des administrateurs")
   
    a. Dans la zone de texte E-mails, entrez l’adresse de messagerie du compte que vous souhaitez approvisionner, puis cliquez sur **Ajouter**.
   
    b. Cliquez sur **Invite**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par UserVoice, pour approvisionner des comptes d’utilisateur AAD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à UserVoice.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à UserVoice, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **UserVoice**.

    ![Lien UserVoice dans la liste des applications](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette UserVoice dans le volet d’accès, vous devez être connecté automatiquement à votre application UserVoice.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png

