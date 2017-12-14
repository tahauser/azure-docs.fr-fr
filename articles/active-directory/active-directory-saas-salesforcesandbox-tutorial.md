---
title: "Didacticiel : intégration d’Azure Active Directory à Salesforce Sandbox | Microsoft Docs"
description: "Découvrez comment configurer une authentification unique entre Azure Active Directory et Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 801088bd63f025ca7cb1f9e4fe66b9c6f7f93453
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Didacticiel : Intégration d’Azure Active Directory à Salesforce Sandbox

Dans ce didacticiel, vous allez apprendre à intégrer Salesforce Sandbox avec Azure Active Directory (Azure AD).

L’intégration de Salesforce Sandbox avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Salesforce Sandbox.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Salesforce Sandbox (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Salesforce Sandbox, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Salesforce Sandbox pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Salesforce Sandbox à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Ajout de Salesforce Sandbox à partir de la galerie
Pour configurer l’intégration de Salesforce Sandbox avec Azure AD, vous devez ajouter Salesforce Sandbox à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Salesforce Sandbox à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Salesforce Sandbox**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. Dans le volet de résultats, sélectionnez **Salesforce Sandbox**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Salesforce Sandbox sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Salesforce Sandbox équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Salesforce Sandbox associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Salesforce Sandbox.

Pour configurer et tester l’authentification unique Azure AD avec Salesforce Sandbox, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)** pour avoir un équivalent de Britta Simon dans Salesforce Sandbox lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Salesforce Sandbox.

**Pour configurer l’authentification unique Azure AD avec Salesforce Sandbox, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Salesforce Sandbox**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Dans la section **Domaine et URL Salesforce Sandbox**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe de support client Salesforce Sandbox](https://help.salesforce.com/support).


4. Si vous avez déjà configuré l’authentification unique pour une autre instance de Salesforce Sandbox dans votre répertoire, vous devez également configurer **l’identificateur** en utilisant la même valeur que pour **l’URL de connexion**. 
    
    >[!Note]
    >Pour afficher le champ **Identificateur**, cochez la case **Afficher les paramètres avancés** dans la page **Configurer l’URL de l’application** de la boîte de dialogue. 


5. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de Salesforce Sandbox**, cliquez sur **Configurer Salesforce Sandbox** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

9. Dans le menu situé en haut, cliquez sur **Setup**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. Dans le volet de navigation de gauche, cliquez sur **Security Controls (Contrôles de sécurité)**, puis sur **Single Sign-On Settings (Paramètres de l’authentification unique)**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. Dans la section Single Sign-On Settings (Paramètres d’authentification unique), procédez comme suit :  ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Sélectionnez **SAML Enabled**. 

     b.  Cliquez sur **Nouveau**.

12. Dans la section SAML Single Sign-On Settings, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a. Dans la zone de texte Name (Nom), indiquez le nom de la configuration (par exemple, *SPSSOWAAD\_Test*). 

    b. Collez la valeur **ID d’entité SAML** dans la zone de texte **Issuer** (Émetteur).

    c. Dans la zone de texte **Entity Id** (ID d’entité), entrez **https://test.salesforce.com** s’il s’agit de la première instance Salesforce Sandbox que vous ajoutez à votre annuaire. Si vous avez déjà ajouté une instance de Salesforce Sandbox, pour **l’ID d’entité**, entrez **l’URL de connexion**, qui doit être au format : `http://company.my.salesforce.com`  
 
    d. Cliquez sur **Parcourir** pour charger le certificat téléchargé.  

    e. Pour **SAML Identity Type (Type d’identité SAML)**, sélectionnez **Assertion contains the Federation ID from the User object (L’assertion contient l’ID de fédération de l’objet utilisateur)**.
 
    f. Pour **SAML Identity Location (Emplacement de l’identité SAML)**, sélectionnez **Identity is in the NameIdentifier element of the Subject statement (L’identité figure dans l’élément NameIdentifier de l’instruction Subject)**.

    g. Collez l’**URL du service d’authentification unique** dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur identité). 

    h. SFDC ne prend pas en charge la déconnexion SAML.  Pour contourner ce problème, collez « https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 » dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité).

    i. Pour **Service Provider Initiated Request Binding (Liaison de demande initiée par le fournisseur de services)**, sélectionnez **HTTP POST**. 

    j. Cliquez sur **Enregistrer**.

### <a name="enable-your-domain"></a>Activer votre domaine
Cette section suppose que vous avez déjà créé un domaine.  Pour plus d’informations, voir [Définition de votre nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Pour activer votre domaine, procédez comme suit :**

1. Dans le volet de navigation gauche, cliquez sur **Domain Management (Gestion des domaines)**, puis sur **My Domain (Mon domaine)**.
   
     ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Vérifiez que votre domaine a été correctement configuré. 

2. Dans la section **Login Page Settings (Paramètres de la page de connexion)**, cliquez sur **Edit (Modifier)**, puis, pour **Authentication Service (Service d’authentification)**, sélectionnez le nom du paramètre d’authentification unique SAML de la section précédente, avant de cliquer sur **Save (Enregistrer)**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Dès qu’un domaine est configuré, vos utilisateurs doivent utiliser l’URL du domaine pour se connecter au sandbox Salesforce.  

Pour obtenir la valeur de l’URL, cliquez sur le profil d’authentification unique créé à la section précédente.    

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Création d’un utilisateur de test Salesforce Sandbox

Dans cette section, un utilisateur nommé Britta Simon est créé dans Salesforce Sandbox. Salesforce Sandbox prend en charge l’approvisionnement juste-à-temps, option activée par défaut.
Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Salesforce Sandbox, un nouveau est créé lorsque vous tentez d’accéder à Salesforce Sandbox.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Salesforce Sandbox.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Salesforce Sandbox, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Salesforce Sandbox**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement de l’utilisateur](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

