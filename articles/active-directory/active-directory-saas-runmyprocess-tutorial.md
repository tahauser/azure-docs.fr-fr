---
title: "Didacticiel : Intégration d’Azure Active Directory à RunMyProcess | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et RunMyProcess."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 3f718e6e93d14df6b71b99dc68fcbcd5e8264b95
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Didacticiel : Intégration d’Azure Active Directory à RunMyProcess

Dans ce didacticiel, vous allez apprendre à intégrer RunMyProcess avec Azure Active Directory (Azure AD).

L’intégration de RunMyProcess à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à RunMyProcess.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à RunMyProcess (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à RunMyProcess, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement RunMyProcess pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez bénéficier de l’[offre d’essai](https://azure.microsoft.com/pricing/free-trial/) d’un mois.

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de RunMyProcess à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-runmyprocess-from-the-gallery"></a>Ajout de RunMyProcess à partir de la galerie
Pour configurer l’intégration de RunMyProcess à Azure AD, vous devez ajouter RunMyProcess à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter RunMyProcess à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **RunMyProcess**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_search.png)

5. Dans le panneau de résultats, sélectionnez **RunMyProcess**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec RunMyProcess sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur RunMyProcess équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur RunMyProcess associé doit être établie.

Dans RunMyProcess, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec RunMyProcess, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test RunMyProcess](#creating-a-runmyprocess-test-user)** pour avoir un équivalent de Britta Simon dans RunMyProcess lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application RunMyProcess.

**Pour configurer l’authentification unique Azure AD avec RunMyProcess, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **RunMyProcess**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

3. Dans la section **Domaine et URL RunMyProcess**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe de support technique RunMyProcess](mailto:support@runmyprocess.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de RunMyProcess**, cliquez sur **Configurer RunMyProcess** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre client RunMyProcess en tant qu’administrateur.

8. Dans le volet de navigation à gauche, cliquez sur **Compte** et sélectionnez **Configuration**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

9. Accédez à la section **Méthode d’authentification** et suivez la procédure ci-dessous :
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Pour **Method**, sélectionnez **SSO with Samlv2**. 

    b. Dans la zone de texte **SSO redirect** (Redirection de SSO), collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Logout redirect** (Redirection de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Name Id Format** (Format d’identificateur de nom), entrez la valeur de **Format de l'identificateur du nom** **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Copiez le contenu du fichier de certificat téléchargé, puis collez-le dans la zone de texte **Certificat**. 
 
    f. Cliquez sur l’icône **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Création d’un utilisateur de test de RunMyProcess

Pour permettre aux utilisateurs Azure AD de se connecter à RunMyProcess, vous devez les approvisionner dans RunMyProcess. Dans le cas de RunMyProcess, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise RunMyProcess en tant qu’administrateur.

2. Dans le volet de navigation à gauche, cliquez sur **Compte** et sélectionnez **Utilisateurs**, puis cliquez sur **Nouvel utilisateur**.
   
    ![Nouvel utilisateur](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nouvel utilisateur")

3. Dans la section **User Settings** , procédez comme suit :
   
    ![Profil](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil") 
  
    a. Tapez le nom et l’adresse électronique d’un compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Name** et **E-mail**. 

    b. Sélectionnez des options pour **IDE language**, **Language** et **Profile**. 

    c. Sélectionnez **Send account creation e-mail to me**. 

    d. Cliquez sur **Enregistrer**.
   
    >[!NOTE]
    >Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par RunMyProcess, pour approvisionner des comptes utilisateur Azure Active Directory. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RunMyProcess.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à RunMyProcess, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **RunMyProcess**.

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque RunMyProcess dans le volet d’accès, vous devez être connecté automatiquement à votre application RunMyProcess.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png

