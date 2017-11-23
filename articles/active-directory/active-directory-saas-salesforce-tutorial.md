---
title: "Didacticiel : Intégration d’Azure Active Directory à Salesforce | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 7471a6999ac4dcd15f5162e69cb0100cc1e3b14f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Didacticiel : Intégration d’Azure Active Directory à Salesforce

Dans ce didacticiel, vous allez apprendre à intégrer Salesforce à Azure Active Directory (Azure AD).

L’intégration de Salesforce dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Salesforce.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Salesforce (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Salesforce, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Salesforce pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Salesforce à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-salesforce-from-the-gallery"></a>Ajout de Salesforce à partir de la galerie
Pour configurer l’intégration de Salesforce avec Azure AD, vous devez ajouter Salesforce à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Salesforce à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Salesforce**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. Dans le volet de résultats, sélectionnez **Salesforce**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Salesforce, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Salesforce correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Salesforce associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Salesforce.

Pour configurer et tester l’authentification unique Azure AD avec Salesforce, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Salesforce](#creating-a-salesforce-test-user)** pour avoir un équivalent de Britta Simon dans Salesforce lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Salesforce.

**Pour configurer l’authentification unique Azure AD avec Salesforce, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Salesforce**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Dans la section **Domaine et URL Salesforce**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : 
   * Compte d’entreprise : `https://<subdomain>.my.salesforce.com`
   * Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez l’[équipe de support technique Salesforce](https://help.salesforce.com/support). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png).

6. Dans la section **Configuration de Salesforce** , cliquez sur **Configurer Salesforce** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.** 

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce.

8.  Sous le volet de navigation **Administrateur**, cliquez sur **Contrôles de sécurité** pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.
    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Si vous ne pouvez pas activer les paramètres de l’authentification unique pour votre compte Salesforce, il vous faudra peut-être contacter [l’équipe du support technique de Salesforce](https://help.salesforce.com/support). 

10. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

      ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. Sur la page **Modifier les paramètres d’authentification unique SAML** , procédez à la configuration suivante :

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Dans le champ **Nom** , entrez un nom convivial pour cette configuration. Le fait d’entrer une valeur pour **Nom** entraîne le remplissage automatique de la zone de texte **Nom API**.

    b. Collez la valeur **ID d’entité SAML** dans le champ **Émetteur** de Salesforce.

    c. Dans la zone de texte **ID d’entité**, tapez votre nom de domaine Salesforce en suivant ce modèle :
      
      * Compte d’entreprise : `https://<subdomain>.my.salesforce.com`
      * Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Cliquez sur **Parcourir** ou **Choisir un fichier** pour ouvrir la boîte de dialogue **Choisir un fichier à télécharger**, sélectionnez votre certificat Salesforce, puis cliquez sur **Ouvrir** pour télécharger le certificat.

    e. Pour **Type d’identité SAML**, sélectionnez **L’assertion contient le nom d’utilisateur de l’utilisateur salesforce.com**.

    f. Pour **Emplacement de l’identité SAML**, sélectionnez **L’identité est dans l’élément NameIdentifier de l’instruction Subject**

    g. Collez **l’URL du service d’authentification unique** dans le champ **URL de connexion au fournisseur d’identité** de Salesforce.
    
    h. Pour **Liaison de demande initiée par le fournisseur de service**, sélectionnez **Redirection HTTP**.
    
    i. Enfin, cliquez sur **Enregistrer** pour appliquer vos paramètres d’authentification unique SAML.

13. Dans le volet de navigation de gauche de Salesforce, cliquez sur **Gestion de domaine** pour développer la section associée, puis cliquez sur **Mon domaine**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Faites défiler le contenu de la fenêtre jusqu’à la section **Configuration de l’authentification**, puis cliquez sur le bouton **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. Dans la section **Service d’authentification**, sélectionnez le nom convivial de votre configuration d’authentification unique SAML, puis cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Si plusieurs services d’authentification sont sélectionnés, les utilisateurs sont invités à choisir le service d’authentification qu’ils préfèrent utiliser pour se connecter lors de l’initialisation d’une authentification unique sur votre environnement Salesforce. Si vous ne voulez pas que cela se produise, vous devez **décocher toutes les cases en regard des autres services d’authentification**.
<CE>    
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée via la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-salesforce-test-user"></a>Création d’un utilisateur de test Salesforce

Dans cette section, un utilisateur appelé Britta Simon est créé dans Salesforce. Salesforce prend en charge l’approvisionnement juste-à-temps, option activée par défaut.
Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Salesforce, un nouveau est créé lorsque vous tentez d’accéder à Salesforce.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Salesforce.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Salesforce, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Salesforce**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Pour tester vos paramètres d’authentification unique, ouvrez le volet d’accès à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **Salesforce**.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement de l’utilisateur](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

