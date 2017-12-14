---
title: "Didacticiel : Intégration d’Azure Active Directory à ClickTime | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et ClickTime."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: f19e1968c736cb21a2a80b9807fa86461e05ee42
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Didacticiel : Intégration d’Azure Active Directory à ClickTime

Dans ce didacticiel, vous allez apprendre à intégrer ClickTime à Azure Active Directory (Azure AD).

L’intégration de ClickTime à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Clicktime
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ClickTime (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec ClickTime, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ClickTime pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ClickTime à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-clicktime-from-the-gallery"></a>Ajout de ClickTime à partir de la galerie
Pour configurer l’intégration de ClickTime à Azure AD, vous devez ajouter ClickTime à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter ClickTime à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **ClickTime**, sélectionnez **ClickTime** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![ClickTime dans la liste des résultats](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ClickTime, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ClickTime équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur ClickTime associé doit être établie.

Dans ClickTime, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ClickTime, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test ClickTime](#create-a-clicktime-test-user)** pour avoir un équivalent de Britta Simon dans ClickTime, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ClickTime.

**Pour configurer l’authentification unique Azure AD avec ClickTime, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **ClickTime**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. Dans la section **Domaine et URL ClickTime**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL comme : `https://app.clicktime.com/sp/`
    
    b. Dans la zone de texte **URL de réponse** , tapez une URL en respectant les formats suivants : 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de ClickTime**, cliquez sur **Configurer ClickTime** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ClickTime en tant qu’administrateur.

8. Dans la barre d’outils située en haut, cliquez sur **Preferences**, puis sur **Security Settings**.

9. Dans la section de configuration **Single Sign-On Preferences** , procédez comme suit :
   
    ![Security Settings](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Security Settings")
   
    a.  Sélectionnez **Autoriser** la connexion à l’aide de l’authentification unique (SSO) avec **Azure AD**.
   
    b. Dans la zone de texte **Identity Provider Endpoint** (Point de terminaison du fournisseur d’identité), collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    c.  Dans le **Bloc-notes**, ouvrez le **certificat codé en base 64** téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone de texte **X.509 Certificate** (Certificat X.509).
   
    d.  Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-clicktime-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-clicktime-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.
 
    ![Bouton Ajouter](./media/active-directory-saas-clicktime-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-clicktime-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-clicktime-test-user"></a>Créer un utilisateur de test ClickTime

Pour se connecter à ClickTime, les utilisateurs d’Azure AD doivent être approvisionnés dans ClickTime.  
Dans le cas de ClickTime, l’approvisionnement est une tâche manuelle.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par ClickTime pour approvisionner des comptes d’utilisateurs Azure AD.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**
1. Connectez-vous à votre client **ClickTime** .
2. Dans la barre d’outils située en haut, cliquez sur **Company**, puis sur **People**.
   
    ![Personnes](./media/active-directory-saas-clicktime-tutorial/tic777282.png "Personnes")
3. Cliquez sur **Add Person**.
   
    ![Add Person](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Add Person")
4. Dans la section New Person, procédez comme suit :
   
    ![Personnes](./media/active-directory-saas-clicktime-tutorial/tic777284.png "Personnes")
   
    a.  Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, **Britta Simon**. 
  
    b.  Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Si vous le souhaitez, vous pouvez définir d’autres propriétés relatives à l’objet de la nouvelle personne.
   
    c.  Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ClickTime.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à ClickTime, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ClickTime**.

    ![Lien ClickTime dans la liste des applications](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ClickTime dans le volet d’accès, vous devez être connecté automatiquement à votre application ClickTime.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png

