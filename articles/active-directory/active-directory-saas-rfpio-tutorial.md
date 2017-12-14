---
title: "Didacticiel : Intégration d’Azure Active Directory à RFPIO | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et RFPIO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 57bb448f1bb858d15a7ecaad5a9ba797f5d7d85e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Didacticiel : Intégration d’Azure Active Directory à RFPIO

Dans ce didacticiel, vous allez apprendre à intégrer RFPIO à Azure Active Directory (Azure AD).

L’intégration de RFPIO dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à RFPIO.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à RFPIO (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec RFPIO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD.
- Un abonnement RFPIO pour lequel l’authentification unique est activée.

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de RFPIO depuis la galerie.
2. Configuration et test de l’authentification unique Azure AD.

## <a name="add-rfpio-from-the-gallery"></a>Ajout de RFPIO depuis la galerie
Pour configurer l’intégration de RFPIO avec Azure AD, vous devez ajouter RFPIO à partir de la galerie à votre liste d’applications SaaS gérées.

### <a name="to-add-rfpio-from-the-gallery"></a>Pour ajouter RFPIO depuis la galerie

1. Dans le panneau de navigation de gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, saisissez **RFPIO**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. Dans le panneau de résultats, sélectionnez **RFPIO**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec RFPIO, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit définir la relation entre l’utilisateur équivalent dans RFPIO et un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur RFPIO associé doit être établie.

Dans RFPIO, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec RFPIO, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test RFPIO](#creating-a-rfpio-test-user)** pour avoir un équivalent de Britta Simon dans RFPIO lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#testing-single-sign-on)** pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application RFPIO.

**Pour configurer l’authentification unique Azure AD avec RFPIO, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **RFPIO**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Dans la section **Domaines et URL RFPIO**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. Dans la zone de texte **Identificateur**, saisissez l’URL : `https://www.rfpio.com`

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Cliquez sur **Afficher les paramètres d’URL avancés**.

    c. Dans la zone de texte **État de relais**, entrez une valeur de chaîne. Contactez l’[équipe de support technique de RFPIO](https://www.rfpio.com/contact/) pour obtenir cette valeur. 

4. Cliquez sur **Afficher les paramètres d’URL avancés**. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** : 

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    Dans la zone de texte **URL d’authentification**, saisissez l’URL : `https://www.app.rfpio.com`

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous au site web **RFPIO** en tant qu’administrateur.

8. Cliquez sur la liste déroulante située en bas à gauche.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Cliquez sur **Paramètres de l’organisation**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Cliquez sur **FONCTIONNALITÉS ET INTÉGRATION**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. Dans **Configuration SAML SSO**, cliquez sur **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. Dans cette section, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Copiez le contenu du **fichier XML de métadonnées téléchargé** et collez-le dans le champ **Configuration de l’identité**.

    > [!NOTE]
    >Pour copier le contenu du fichier **XML de métadonnées** téléchargé, utilisez **Notepad++** ou **XML Editor**. 

    b. Cliquez sur **Valider**.

    c. Après avoir cliqué sur **Valider**, activez **SAML(Enabled)**.

    d. Cliquez sur **Envoyer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-rfpio-test-user"></a>Créer un utilisateur de test RFPIO

Pour se connecter à RFPIO, les utilisateurs d’Azure AD doivent être configurés dans RFPIO.  
Dans le cas de RFPIO, l’approvisionnement se fait manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise RFPIO en tant qu’administrateur.

2. Cliquez sur la liste déroulante située en bas à gauche.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Cliquez sur **Paramètres de l’organisation**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Cliquez sur **MEMBRES DE L’ÉQUIPE**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Cliquez sur **AJOUTER DES MEMBRES**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. Dans la section **Ajouter de nouveaux membres**. Procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Entrez l’**adresse e-mail** dans le champ **Entrer une adresse e-mail par ligne**.

    b. Sélectionnez le **Rôle** selon vos besoins.

    c. Cliquez sur **AJOUTER DES MEMBRES**.
        
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RFPIO.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à RFPIO, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **RFPIO**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette RFPIO dans le panneau d’accès, vous devez être connecté automatiquement à votre application RFPIO.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur les procédures d’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png

