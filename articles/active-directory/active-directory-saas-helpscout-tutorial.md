---
title: "Didacticiel : Intégration d’Azure Active Directory à Help Scout | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 5608df300ad3b5d3fd42bd904a52d43d115ebf53
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Didacticiel : Intégration d’Azure Active Directory à Help Scout

Dans ce didacticiel, vous allez apprendre à intégrer Help Scout à Azure Active Directory (Azure AD).

L’intégration de Help Scout à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Help Scout.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Help Scout (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Help Scout, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Help Scout pour lequel l’authentification unique est activée

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Help Scout à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-help-scout-from-the-gallery"></a>Ajout de Help Scout à partir de la galerie
Pour configurer l’intégration de Help Scout à Azure AD, vous devez ajouter Help Scout à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Help Scout à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Help Scout**, sélectionnez **Help Scout** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Help Scout dans la liste des résultats](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Help Scout avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Help Scout équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Help Scout associé doit être établie.

Comme Help Scout utilise des adresses e-mail pour les connexions, utilisez la même **adresse e-mail** en tant que **nom d’utilisateur** dans Azure AD pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Help Scout, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Help Scout](#create-a-help-scout-test-user)** pour avoir un équivalent de Britta Simon dans Help Scout lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Help Scout.

**Pour configurer l’authentification unique Azure AD avec Help Scout, effectuez les étapes suivantes :** 

1. Dans le portail Azure, dans la page d’intégration de l’application **Help Scout**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Dans la section **Domaine et URL Help Scout**, suivez les étapes ci-dessous pour configurer l’application en mode initié par le **fournisseur d’identité** :

    ![Informations d’authentification unique dans Domaine et URL Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identificateur** correspond à l’**« URI d’audience (ID d’entité de fournisseur de services) »** Help Scout, qui commence par `urn:`

    b. **URL de réponse** correspond à l’**« URL de publication (URL Assertion Consumer Service) »** Help Scout, qui commence par `https://` 

    > [!NOTE] 
    > Les valeurs de ces URL servent uniquement à des fins de démonstration. Vous devez mettre à jour ces valeurs avec l’URL de réponse et l’identificateur réels. Vous obtenez ces valeurs à partir de l’onglet **Authentification unique** sous la section Authentification, qui est décrite plus loin dans le didacticiel.

4. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de services**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez l’étape suivante :

    ![Informations d’authentification unique dans Domaine et URL Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://secure.helpscout.net/members/login/`
     
5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. Dans la section **Configuration de Help Scout**, cliquez sur **Configurer Help Scout** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Help Scout en tant qu’administrateur.

9. Une fois que vous êtes connecté, cliquez sur **Gérer** dans le menu supérieur, puis sélectionnez **Company** (Société) dans le menu déroulant.

    ![Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Sélectionnez **Authentification** dans le menu de gauche. 

    ![Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Vous accédez ainsi à la section des paramètres SAML où vous effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Copiez la valeur **URL de publication (URL Assertion Consumer Service)** et collez-la dans la zone **URL de réponse** dans le portail Azure, sous la section **Domaine et URL** Help Scout.
    
    b. Copiez la valeur **URI d’audience (ID d’entité de fournisseur de services)** et collez-la dans la zone **Identificateur** dans le portail Azure, sous la section **Domaine et URL** Help Scout.

12. Activez **Activer SAML** et effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. Dans la zone de texte **URL d’authentification unique**, collez la valeur de l’**URL du service d’authentification unique** que vous avez copiée à partir du portail Azure.
    
    b. Cliquez sur **Charger le certificat** pour charger le **certificat (en base64)** téléchargé à partir du portail Azure.

    c. Entrez les domaines de messagerie de votre organisation, par exemple `contoso.com`, dans la zone de texte **Email Domains** (Domaines de messagerie). Vous pouvez séparer plusieurs domaines par une virgule. À tout moment, un utilisateur ou administrateur Help Scout qui entre dans ce domaine spécifique dans la [page de connexion d’Help Scout](https://secure.helpscout.net/members/login/) est acheminé vers le fournisseur d’identité pour être authentifié avec ses informations d’identification.

    d. Enfin, vous pouvez activer **Force SAML Sign-on** (Forcer l’authentification SAML) si vous souhaitez que les utilisateurs se connectent uniquement à Help Scout via cette méthode. Si vous voulez toujours leur donner la possibilité de se connecter avec leurs informations d’identification Help Scout, vous pouvez laisser cette option désactivée. Même si elle est activée, un propriétaire de compte sera toujours en mesure de se connecter à Help Scout avec son mot de passe de compte.

    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-help-scout-test-user"></a>Créer un utilisateur de test Help Scout

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Help Scout. Help Scout prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. S’il n’y a pas déjà un utilisateur dans Help Scout, un nouvel utilisateur est créé lorsque vous tentez d’y accéder.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Help Scout.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à Help Scout, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Help Scout**.

    ![Lien Help Scout dans la liste des applications](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Help Scout dans le volet d’accès, vous devez être connecté automatiquement à votre application Help Scout.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

