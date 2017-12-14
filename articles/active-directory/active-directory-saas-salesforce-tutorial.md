---
title: "Didacticiel : Intégration d’Azure Active Directory à Salesforce | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: ed127afbca5135ade21f6ac53d18d46e88939fd9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Didacticiel : Intégration d’Azure Active Directory à Salesforce

Dans ce didacticiel, vous allez apprendre à intégrer Salesforce à Azure Active Directory (Azure AD).

L’intégration de Salesforce dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Salesforce.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Salesforce (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Salesforce, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Salesforce pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Salesforce à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-salesforce-from-the-gallery"></a>Ajout de Salesforce à partir de la galerie
Pour configurer l’intégration de Salesforce avec Azure AD, vous devez ajouter Salesforce à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Salesforce à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Salesforce**, sélectionnez **Salesforce** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Salesforce dans la liste des résultats](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Salesforce sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Salesforce correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Salesforce associé doit être établie.

Dans Salesforce, attribuez la valeur du **nom d’utilisateur** d’Azure AD au **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Salesforce, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Salesforce](#create-a-salesforce-test-user)** pour disposer dans Salesforce d’un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Salesforce.

**Pour configurer l’authentification unique Azure AD avec Salesforce, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Salesforce**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Dans la section **Domaine et URL Salesforce**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant :
    
    Compte d’entreprise : `https://<subdomain>.my.salesforce.com`

    Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. Dans la zone de texte **Identificateur**, entrez la valeur au format suivant :
    
    Compte d’entreprise : `https://<subdomain>.my.salesforce.com`

    Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Salesforce](https://help.salesforce.com/support).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Salesforce** , cliquez sur **Configurer Salesforce** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce.

8. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Dans le volet de navigation, accédez à **SETTINGS** et cliquez sur **Identity** pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Si vous ne pouvez pas activer les paramètres de l’authentification unique pour votre compte Salesforce, il vous faudra peut-être contacter [l’équipe du support technique de Salesforce](https://help.salesforce.com/support). 

11. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

      ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. Sur la page **Modifier les paramètres d’authentification unique SAML** , procédez à la configuration suivante :

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Dans le champ **Nom** , entrez un nom convivial pour cette configuration. Le fait d’entrer une valeur pour **Nom** entraîne le remplissage automatique de la zone de texte **Nom API**.

    b. Dans le champ **Issuer**, collez la valeur de **ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **ID d’entité**, tapez votre nom de domaine Salesforce en suivant ce modèle :
      
      * Compte d’entreprise : `https://<subdomain>.my.salesforce.com`
      * Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Dans **Identity Provider Certificate**, cliquez sur **Choose File** pour sélectionner le fichier de certificat que vous avez téléchargé à partir du portail Azure.

    e. Comme **SAML Identity Type**, choisissez l’une des options suivantes :
    
      * Sélectionnez **Assertion contains the User’s Salesforce username** si le Salesforce Username de l’utilisateur est passé dans l’assertion SAML.

      * Sélectionnez **Assertion contains the Federation ID from the User object** si le Federation ID de l’objet User est passé dans l’assertion SAML.

      * Sélectionnez **Assertion contains the Use ID from the User object** si le Federation ID de l’objet User est passé dans l’assertion SAML.

    f. Pour **Emplacement de l’identité SAML**, sélectionnez **L’identité est dans l’élément NameIdentifier de l’instruction Subject**.

    g. Pour **Liaison de demande initiée par le fournisseur de service**, sélectionnez **Redirection HTTP**.

    h. Dans la zone de texte **Identity Provider Login URL**, collez la valeur de l’**URL du service d’authentification unique** que vous avez copiée à partir du portail Azure.
    
    i. Enfin, cliquez sur **Enregistrer** pour appliquer vos paramètres d’authentification unique SAML.

14. Dans le volet de navigation de gauche de Salesforce, cliquez sur **Company Settings** pour développer la section associée, puis cliquez sur **My Domain**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Faites défiler le contenu de la fenêtre jusqu’à la section **Configuration de l’authentification**, puis cliquez sur le bouton **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. Dans la section **Configuration de l’authentification**, cochez **Page de connexion** comme **Service d’authentification** de votre configuration SSO SAML, puis cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Si plusieurs services d’authentification sont sélectionnés, les utilisateurs sont invités à choisir le service d’authentification qu’ils préfèrent utiliser pour se connecter lors de l’initialisation d’une authentification unique sur votre environnement Salesforce. Si vous ne voulez pas que cela se produise, vous devez **décocher toutes les cases en regard des autres services d’authentification**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-salesforce-test-user"></a>Créer un utilisateur de test Salesforce

Dans cette section, un utilisateur appelé Britta Simon est créé dans Salesforce. Salesforce prend en charge l’approvisionnement juste-à-temps, option activée par défaut.
Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Salesforce, un nouveau est créé lorsque vous tentez d’accéder à Salesforce.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Salesforce.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Salesforce, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Salesforce**.

    ![Lien Salesforce de la liste des applications](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Salesforce dans le volet d’accès, vous êtes automatiquement connecté à votre application Salesforce.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



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

