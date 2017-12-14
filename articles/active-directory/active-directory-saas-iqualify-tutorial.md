---
title: "Didacticiel : Intégration d’Azure Active Directory à iQualify LMS | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et iQualify LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 6f8a7b7fd155a6ad0df7cb1f9026b4acca2401cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Didacticiel : Intégration d’Azure Active Directory à iQualify LMS

Dans ce didacticiel, vous allez apprendre à intégrer iQualify LMS avec Azure Active Directory (Azure AD).

L’intégration d’iQualify LMS avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à iQualify LMS.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à iQualify LMS (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec iQualify LMS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à iQualify LMS pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’iQualify LMS à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-iqualify-lms-from-the-gallery"></a>Ajout d’iQualify LMS à partir de la galerie
Pour configurer l’intégration d’iQualify LMS avec Azure AD, vous devez ajouter iQualify LMS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter iQualify LMS à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **iQualify LMS**, sélectionnez **iQualify LMS** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![iQualify LMS dans la liste des résultats](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iQualify LMS, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur iQualify LMS équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur iQualify LMS associé doit être établie.

Dans iQualify LMS, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec iQualify LMS, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test iQualify LMS](#create-an-iqualify-lms-test-user)** pour avoir un équivalent de Britta Simon dans iQualify LMS lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application iQualify LMS.

**Pour configurer l’authentification unique Azure AD avec iQualify LMS, effectuez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **iQualify LMS**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Dans la section **Domaines et URL iQualify LMS**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par IDP :

    ![Informations d’authentification unique dans Domaine et URL iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : 
    | |
    |--|--|
    | Environnement de production : `https://<yourorg>.iqualify.com/`|
    | Environnement de test : `https://<yourorg>.iqualify.io`|
    
    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : 
    | |
    |--|--|
    | Environnement de production : `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Environnement de test : `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant :
    | |
    |--|--|
    | Environnement de production : `https://<yourorg>.iqualify.com/login` |
    | Environnement de test : `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’iQualify LMS](https://www.iqualify.com). 

5. L’application iQualify LMS attend les assertions Security Assertion Markup Language (SAML) à afficher dans un format spécifique. Configurez les revendications et gérez les valeurs des attributs dans la section **Attributs utilisateur** de la page d’intégration de l’application iQualify LMS, comme illustré dans la capture d’écran suivante :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, effectuez les étapes suivantes pour chaque ligne indiquée dans le tableau ci-dessous :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | « votre attribut » | 

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

    e. Répétez les étapes « a » à « d » pour les lignes suivantes du tableau. 

    > [!Note]
    > La répétition des étapes « a » à « d » pour l’attribut **person_id** est **facultative**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (en base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. Dans la section **Configuration de iQualify LMS** , cliquez sur **Configurer iQualify LMS** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration d’iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Ouvrez une nouvelle fenêtre de navigateur, puis connectez-vous à votre environnement iQualify LMS en tant qu’administrateur.

11. Une fois connecté, cliquez sur votre avatar en haut à droite, puis sur **« Account settings ».**

    ![Account settings](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. Dans la zone de paramètres de compte, cliquez sur le menu de ruban sur la gauche et cliquez sur **« INTEGRATIONS ».**
    
    ![INTEGRATIONS](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. Sous INTEGRATIONS, cliquez sur l’icône **SAML**.

    ![Icône SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![Paramètres d’authentification SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. Dans la zone **SAML SINGLE SIGN-ON SERVICE URL**, entrez la valeur de la zone **URL du service d’authentification unique SAML** copiée à partir de la fenêtre Configuration de l’application Azure AD.
    
    b. Dans la zone **SAML LOGOUT URL**, collez la valeur de la zone **URL de déconnexion** copiée à partir de la fenêtre Configuration de l’application Azure AD.
    
    c. Ouvrez le fichier de certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone **PUBLIC CERTIFICATE**.
    
    d. Dans **LOGIN BUTTON LABEL**, entrez le nom du bouton à afficher dans la page de connexion.
    
    e. Cliquez sur **ENREGISTRER**.

    f. Cliquez sur **UPDATE**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Créer un utilisateur de test iQualify LMS

Dans cette section, un utilisateur appelé Britta Simon est créé dans iQualify. iQualify LMS prend en charge l’approvisionnement d’utilisateur juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans iQualify, un nouvel utilisateur est créé quand vous tentez d’y accéder.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iQualify LMS.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à iQualify LMS, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **iQualify LMS**.

    ![Lien iQualify LMS dans la liste des applications](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette iQualify LMS dans le panneau d’accès, la page de connexion de l’application iQualify LMS doit s’afficher. 

   ![page de connexion](./media/active-directory-saas-iqualify-tutorial/login.png) 

Cliquez sur le bouton **Sign in with Azure AD**. Vous devriez être authentifié automatiquement auprès de votre application iQualify LMS.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png

