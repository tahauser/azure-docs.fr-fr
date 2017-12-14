---
title: "Didacticiel : Intégration d’Azure Active Directory à Netsuite | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Netsuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: 23e39f20f5b3faa4f6cfba57508d1649e2bbdaa3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Didacticiel : Intégration d’Azure Active Directory à Netsuite

Dans ce didacticiel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) avec Netsuite.

L’intégration de Netsuite avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Netsuite.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Netsuite (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Netsuite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Netsuite pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Netsuite à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-netsuite-from-the-gallery"></a>Ajout de Netsuite à partir de la galerie
Pour configurer l’intégration de Netsuite à Azure AD, vous devez ajouter Netsuite à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Netsuite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Netsuite**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. Dans le panneau des résultats, sélectionnez **Netsuite**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Netsuite sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Netsuite équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Netsuite associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Netsuite.

Pour configurer et tester l’authentification unique Azure AD avec Netsuite, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Netsuite](#creating-a-netsuite-test-user)** pour avoir un équivalent de Britta Simon dans Netsuite lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Netsuite.

**Pour configurer l’authentification unique Azure AD avec Netsuite, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Netsuite**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. Dans la section **Domaine et URL Netsuite**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :   `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse réel. Pour obtenir ces valeurs, contactez [l’équipe de support technique Netsuite](http://www.netsuite.com/portal/services/support.shtml).
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Netsuite**, cliquez sur **Configurer Netsuite** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise Netsuite en tant qu’administrateur.

8. Dans la barre d’outils située en haut de la page, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.

    ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Dans la liste **Tâches de configuration**, sélectionnez **Intégration**.

    ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. Dans la section **Gérer l’authentification**, cliquez sur **Authentification unique SAML**.

    ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. Sur la page **Configuration SAML** , procédez comme suit :
   
    a. Copiez la valeur **URL du service d’authentification unique SAML** à partir de la section **Référence rapide** de **Configurer l’authentification**, puis collez-la dans le champ **Identity Provider Login Page** (Page de connexion du fournisseur d’identité) de Netsuite.

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. Dans Netsuite, sélectionnez **Méthode d’authentification principale**.

    c. Pour le champ intitulé **Métadonnées du fournisseur d’identité SAMLV2**, sélectionnez **Charger un fichier de métadonnées IDP**. Cliquez ensuite sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Cliquez sur **Envoyer**.

12. Dans Azure AD, activez la case à cocher **Afficher et modifier tous les autres attributs utilisateur**, puis ajoutez un attribut.

    ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Dans le champ **Nom de l’attribut**, entrez `account`. Dans le champ **Valeur de l’attribut** , entrez votre ID de compte Netsuite. Cette valeur est constante et varie selon le compte. Vous trouverez ci-dessous des instructions sur la recherche de votre ID de compte :

      ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. Dans Netsuite, dans le menu de navigation supérieur, cliquez sur **Configurer**.

    b. Cliquez ensuite sous la section **Tâches de configuration** du menu de navigation situé à gauche, sélectionnez la section **Intégration**, puis cliquez sur **Préférences de services web**.

    c. Copiez votre ID de compte Netsuite et collez-le dans le champ **Valeur de l’attribut** dans Azure AD.

    ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Avant que les utilisateurs puissent utiliser l’authentification unique dans Netsuite, vous devez d’abord leur affecter les autorisations appropriées dans Netsuite. Procédez comme suit pour attribuer ces instructions.

    a. Dans le menu de navigation principal, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.
      
      ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les rôles**.
      
      ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Cliquez sur **Nouveau rôle**.

    d. Entrez un **Nom** pour le nouveau rôle, puis cochez la case **Authentification unique seulement**.
      
      ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Cliquez sur **Enregistrer**.

    f. Dans le menu situé en haut, cliquez sur **Autorisations**. Cliquez sur **Configuration**.
      
       ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Sélectionnez **Configurer l’authentification unique SAM**, puis cliquez sur **Ajouter**.

    h. Cliquez sur **Enregistrer**.

    i. Dans le menu de navigation principal, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.
      
       ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les utilisateurs**.
      
       ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Sélectionnez un utilisateur de test. Puis cliquez sur **Modifier**.
      
       ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. Dans la boîte de dialogue Rôles, sélectionnez le rôle que vous avez créé et cliquez sur **Ajouter**.
      
       ![Configurer l’authentification unique](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Cliquez sur **Enregistrer**.
    
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 

### <a name="creating-a-netsuite-test-user"></a>Création d’un utilisateur de test Netsuite

Dans cette section, un utilisateur nommé Britta Simon est créé dans Netsuite. Netsuite prend en charge l’approvisionnement juste-à-temps, option activée par défaut.
Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Netsuite, un nouveau est créé lorsque vous tentez d’accéder à Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Netsuite.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Netsuite, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Netsuite**.

    ![Configurer l’authentification unique](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Pour tester vos paramètres d’authentification unique, ouvrez le volet d’accès à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **Netsuite**.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement de l’utilisateur](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

