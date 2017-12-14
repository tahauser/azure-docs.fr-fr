---
title: "Didacticiel : Intégration d’Azure Active Directory à Image Relay | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Image Relay."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: da05da676ea160dcdaf7e9c711d5f308ac260434
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Didacticiel : Intégration d’Azure Active Directory à ImageRelay

Dans ce didacticiel, vous allez apprendre à intégrer Image Relay avec Azure Active Directory (Azure AD).

L’intégration d’Image Relay à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Image Relay.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Image Relay (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Image Relay, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Image Relay pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Image Relay à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-image-relay-from-the-gallery"></a>Ajout d’Image Relay à partir de la galerie
Pour configurer l’intégration d’Image Relay avec Azure AD, vous devez ajouter Image Relay à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Image Relay à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Image Relay**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. Dans le volet de résultats, sélectionnez **Image Relay**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Image Relay, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Image Relay équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Image Relay associé doit être établie.

Dans Image Relay, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Image Relay, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Image Relay](#creating-an-image-relay-test-user)** pour avoir un équivalent de Britta Simon dans Image Relay lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Image Relay.

**Pour configurer l’authentification unique Azure AD avec Image Relay, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Image Relay**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. Dans la section **Domaine et URL Image Relay**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.imagerelay.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’Image Relay](http://support.imagerelay.com/). 
 


4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Image Relay**, cliquez sur **Configurer Image Relay** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL du service de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Image Relay en tant qu’administrateur.

8. Dans la barre d’outils du haut, cliquez sur la charge de travail **Utilisateurs et autorisations**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Cliquez sur **Créer une nouvelle autorisation**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. Dans la charge de travail **Paramètres d’authentification unique**, sélectionnez la case à cocher **Ce groupe peut se connecter uniquement via l’authentification unique**, puis cliquez sur **Enregistrer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Accédez à **Paramètres du compte**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Accédez à la charge de travail **Paramètres d’authentification unique** .
    
     ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. Dans la boîte de dialogue **SAML Settings** (Paramètres SAML), procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. Dans la zone de texte **URL de connexion**, collez la valeur de **l’URL du service d’authentification unique** copiée à partir du portail Azure.

    b. Dans la zone de texte **URL de déconnexion**, collez la valeur de **l’URL du service de déconnexion unique** copiée à partir du portail Azure.

    c. Sous **Name Id Format** (Format d’ID de nom), sélectionnez **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Sous **Binding Options for Requests from the Service Provider (Image Relay)** (Options de liaison pour les demandes provenant du fournisseur de services (ImageRelay)), sélectionnez **POST Binding** (Liaison POST).

    e. Sous **Certificat x.509**, cliquez sur **Mettre à jour le certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte Certificat X.509.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Dans la section **Approvisionnement juste à temps des utilisateurs**, sélectionnez la case à cocher **Activer l’approvisionnement juste à temps des utilisateurs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Sélectionnez le groupe d’autorisations (par exemple, **SSO de base**) qui sera autorisé à se connecter uniquement via l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-image-relay-test-user"></a>Création d’un utilisateur de test Image Relay

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Image Relay.

**Pour créer un utilisateur appelé Britta Simon dans Image Relay, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Image Relay tant qu’administrateur.

2. Accédez à **Utilisateurs et autorisations** et sélectionnez **Créer un utilisateur SSO**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Renseignez les champs **E-mail**, **Prénom**, **Nom** et **Société** de l’utilisateur que vous souhaitez configurer, puis sélectionnez le groupe d’autorisations (par exemple authentification unique de base), qui peut se connecter uniquement via l’authentification unique.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Cliquez sur **Create**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Image Relay.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Image Relay, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Image Relay**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.    

Lorsque vous cliquez sur la mosaïque Image Relay dans le panneau d’accès, vous devriez être connecté automatiquement à votre application Image Relay.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

