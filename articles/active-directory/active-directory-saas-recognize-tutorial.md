---
title: "Didacticiel : intégration d’Azure Active Directory à Recognize | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Recognize."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: cf0945b478d284da0519a6da49f22f0940b80944
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Didacticiel : Intégration d’Azure Active Directory à Recognize

Dans ce didacticiel, vous allez apprendre à intégrer Recognize avec Azure Active Directory (Azure AD).

L’intégration de Recognize à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Recognize
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Recognize (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Recognize, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Recognize pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Recognize à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-recognize-from-the-gallery"></a>Ajout de Recognize à partir de la galerie
Pour configurer l’intégration de Recognize à Azure AD, vous devez ajouter Recognize à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Recognize à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Recognize**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. Dans le volet de résultats, sélectionnez **Recognize**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Recognize sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Recognize équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Recognize associé doit être établie.

Dans Recognize, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec Recognize, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Recognize](#creating-a-recognize-test-user)** pour avoir un équivalent de Britta Simon dans Recognize lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Recognize.

**Pour configurer l’authentification unique Azure AD avec Recognize, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Recognize**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. Dans la section **Domaine et URL Recognize**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://recognizeapp.com/<your-domain>/saml/sso`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez l’[équipe de support technique Recognize](mailto:support@recognizeapp.com) pour obtenir l’URL de connexion. Vous pouvez obtenir la valeur de l’identificateur en ouvrant l’URL des métadonnées du fournisseur de service dans la section Paramètres d’authentification unique décrite plus loin dans le didacticiel. . 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Recognize**, cliquez sur **Configurer Recognize** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Recognize en tant qu’administrateur.

8. Dans l’angle supérieur droit, cliquez sur **Menu**. Accédez à **Administrateur d’entreprise**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. Dans le volet de navigation gauche, cliquez sur **Paramètres**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. Dans la section **Paramètres d’authentification unique** , procédez comme suit.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. Pour **Activer l’authentification unique**, sélectionnez **Activé**.

    b. Dans la zone de texte **ID d’entité IDP**, collez la valeur **ID d’entité SAML** que vous avez copiée à partir du portail Azure.
    
    c. Dans la zone de texte **Sso target url** (URL cible de l’authentification unique), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
    
    d. Dans la zone de texte **Slo target url** (URL cible de SLO), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure. 
    
    e. Ouvrez dans le Bloc-notes votre fichier **Certificate(Base64)** téléchargé, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificate** (Certificat).
    
    f. Cliquez sur le bouton **Enregistrer les paramètres**. 

11. En regard de la section **Paramètres d’authentification unique**, copiez l’URL sous **URL de métadonnées du fournisseur de services**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. Ouvrez le **lien URL de métadonnées** dans un navigateur vide pour télécharger le document de métadonnées. Copiez la valeur EntityDescriptor value(entityID) à partir du fichier, puis collez-la vers la zone de texte **Identificateur** dans la section **Domaine et URL Recognize** sur le portail Azure.
    
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-recognize-test-user"></a>Création d’un utilisateur de test Recognize

Pour se connecter à Recognize, les utilisateurs d’Azure AD doivent être approvisionnés dans Recognize. Dans le cas de Recognize, l’approvisionnement est une tâche manuelle.

Cette application ne prend pas en charge l’approvisionnement SCIM, mais inclut une autre synchronisation de l’autre utilisateur qui approvisionne les utilisateurs. 

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Recognize en tant qu’administrateur.

2. Dans l’angle supérieur droit, cliquez sur **Menu**. Accédez à **Administrateur d’entreprise**.

3. Dans le volet de navigation gauche, cliquez sur **Paramètres**.

4. Dans la section **Synchronisation de l’utilisateur**, procédez comme suit.
   
   ![Nouvel utilisateur](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Nouvel utilisateur")
   
   a. Pour l’option **Synchronisation activée**, sélectionnez **Activé**.
   
   b. Pour **Choisir le fournisseur de synchronisation**, sélectionnez **Microsoft / Office 365**.
   
   c. Cliquez sur **Run User Sync (Exécuter la synchronisation des utilisateurs)**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Recognize.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Recognize, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Recognize**.

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Recognize dans le volet d’accès, vous devez être connecté automatiquement à votre application Recognize. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png

