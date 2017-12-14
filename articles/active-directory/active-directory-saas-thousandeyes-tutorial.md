---
title: "Didacticiel : intégration d’Azure Active Directory à ThousandEyes | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et ThousandEyes."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: jeedes
ms.openlocfilehash: 81132e40eba589dff7ede0ee94fab1a289dc19d7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Didacticiel : Intégration d’Azure AD à ThousandEyes

Dans ce didacticiel, vous allez apprendre à intégrer ThousandEyes à Azure Active Directory (Azure AD).

L’intégration de ThousandEyes à Azure AD vous fait bénéficier des avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ThousandEyes.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ThousandEyes (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à ThousandEyes, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ThousandEyes pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ThousandEyes à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-thousandeyes-from-the-gallery"></a>Ajout de ThousandEyes à partir de la galerie
Pour configurer l’intégration de ThousandEyes à Azure AD, vous devez ajouter ThousandEyes à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ThousandEyes à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **ThousandEyes**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. Dans le volet de résultats, sélectionnez **ThousandEyes**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de ThousandEyes avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ThousandEyes équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur ThousandEyes associé doit être établie.

Dans ThousandEyes, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ThousandEyes, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test ThousandEyes](#creating-a-thousandeyes-test-user)** pour avoir un équivalent de Britta Simon dans ThousandEyes lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ThousandEyes.

**Pour configurer l’authentification unique Azure AD auprès de ThousandEyes, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **ThousandEyes**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Dans la section **Domaine et URL ThousandEyes**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://app.thousandeyes.com/login/sso`

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de ThousandEyes**, cliquez sur **Configurer ThousandEyes** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **ThousandEyes** en tant qu’administrateur.

8. Dans le menu situé en haut, cliquez sur **Settings**.
   
    ![Paramètres](./media/active-directory-saas-thousandeyes-tutorial/ic790066.png "Paramètres")

9. Cliquez sur **Account**
   
    ![Compte](./media/active-directory-saas-thousandeyes-tutorial/ic790067.png "Compte")

10. Cliquez sur l’onglet **Security & Authentication (Sécurité et authentification)**.
   
    ![Sécurité et authentification](./media/active-directory-saas-thousandeyes-tutorial/ic790068.png "Sécurité et authentification")

11. Dans la section **Setup Single Sign-On** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/ic790069.png "Configurer l’authentification unique")
  
    a. Sélectionnez **Activer l'authentification unique**.
  
    b. Dans la zone de texte **URL de la page de connexion**, collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
  
    c. Dans la zone de texte **URL de la page de déconnexion**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
  
    d. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez l’**ID d’entité SAML** copié à partir du portail Azure.
  
    e. Dans **Certificat de vérification**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure.
  
    f. Cliquez sur **Save**.
 
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-thousandeyes-test-user"></a>Création d’un utilisateur de test ThousandEyes

Pour se connecter à ThousandEyes, les utilisateurs d’Azure AD doivent être approvisionnés dans ThousandEyes.  
Dans le cas de ThousandEyes, l’approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur, fournis par ThousandEyes, pour approvisionner des comptes d’utilisateur Azure Active Directory.

**Pour approvisionner un compte d’utilisateur dans ThousandEyes, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise ThousandEyes en tant qu’administrateur.

2. Cliquez sur **Settings**.
   
    ![Paramètres](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Paramètres")

3. Cliquez sur **Account**.
   
    ![Compte](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Compte")

4. Cliquez sur l’onglet **Accounts & Users (Comptes et utilisateurs)**.
   
    ![Comptes et utilisateurs](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Comptes et utilisateurs")

5. Dans la section **Add Users & Accounts (Ajouter des utilisateurs et des comptes)**, procédez comme suit :
   
    ![Ajouter des comptes d’utilisateurs](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Ajouter des comptes d’utilisateurs")   
  
    a. Dans la zone de texte **Name**, tapez le nom complet d’un utilisateur, par exemple **Britta Simon**.

    b. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple, **brittasimon@contoso.com**.
   
    b. Cliquez sur le bouton **Add New User to Account**.
      
     >[!NOTE]
     >Le titulaire du compte Azure Active Directory reçoit un courrier électronique contenant un lien permettant de confirmer et activer le compte.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ThousandEyes.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à ThousandEyes, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ThousandEyes**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette ThousandEyes dans le volet d’accès, vous devez vous connecter automatiquement à votre application ThousandEyes.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_203.png

