---
title: "Didacticiel : Intégration d’Azure Active Directory à Rally Software | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Rally Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 9e3b5ad4487ff1309923a1b0ffac9589084e715b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Didacticiel : Intégration d’Azure Active Directory à Rally Software

Dans ce didacticiel, vous allez apprendre à intégrer Rally Software à Azure Active Directory (Azure AD).

L’intégration de Rally Software à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Rally Software.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Rally Software (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Rally Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Rally Software pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Rally Software à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-rally-software-from-the-gallery"></a>Ajout de Rally Software à partir de la galerie
Pour configurer l’intégration de Rally Software à Azure AD, vous devez ajouter Rally Software à partir de la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Rally Software à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Rally Software**, sélectionnez **Rally Software** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Rally Software dans la liste des résultats](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Rally Software à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Rally Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Rally Software associé doit être établi.

Dans Rally Software, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Rally Software, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Rally Software](#create-a-rally-software-test-user)** pour avoir un équivalent de Britta Simon dans Rally Software lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Rally Software.

**Pour configurer l’authentification unique Azure AD avec Rally Software, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Rally Software**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. Dans la section **Domaine et URL Rally Software**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenant-name>.rally.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support Rally Software](https://help.rallydev.com/). 
 


4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Rally Software**, cliquez sur **Configurer Rally Software** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Connectez-vous à votre locataire **Rally Software** .

8. Dans la barre d’outils située en haut, cliquez sur **Setup**, puis sélectionnez **Subscription**.
   
    ![Subscription](./media/active-directory-saas-rally-software-tutorial/ic769531.png "Subscription")

9. Cliquez sur le bouton **Action**. Sélectionnez **Modifier l’abonnement** en haut à droite de la barre d’outils.

10. Dans la page **Subscription**, procédez comme suit, puis cliquez sur **Save & Close** :
   
    ![Authentication](./media/active-directory-saas-rally-software-tutorial/ic769542.png "Authentication")
   
    a. Sélectionnez **Rally or SSO authentication** dans la liste déroulante Authentication.

    b. Dans la zone de texte **Identity provider URL** (URL du fournisseur d’identité), collez **l’ID d’entité SAML** copié à partir du portail Azure. 

    c. Dans la zone de texte **SSO Logout** (Déconnexion SSO), collez **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-rally-software-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-rally-software-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-rally-software-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-rally-software-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-rally-software-test-user"></a>Créer un utilisateur de test Rally Software

Pour que les utilisateurs Azure AD puissent se connecter, ils doivent être attribués dans l’application Rally Software avec leur nom d’utilisateur Azure Active Directory.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre locataire Rally Software.

2. Accédez à **Setup \> USERS**, puis cliquez sur **+ Add New**.
   
    ![Utilisateurs](./media/active-directory-saas-rally-software-tutorial/ic781039.png "Utilisateurs")

3. Tapez le nom dans la zone de texte New User, puis cliquez sur **Add with Details**.

4. Dans la section **Create User** , procédez comme suit :
   
    ![Create User](./media/active-directory-saas-rally-software-tutorial/ic781040.png "Create User")

    a. Dans la zone de texte **User Name** (Nom d’utilisateur), tapez le nom complet d’un utilisateur, par exemple **Britta Simon**.
   
    b. Dans la zone de texte **E-mail Address** (Adresse e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    c. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    d. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.

    e. Cliquez sur **Enregistrer et fermer**.

   >[!NOTE]
   >Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Rally Software pour approvisionner des comptes d’utilisateur Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Rally Software.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à Rally Software, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Rally Software**.

    ![Lien Rally Software dans la liste des applications](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Rally Software dans le panneau d’accès, vous devez être connecté automatiquement à votre application Rally Software.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_203.png

