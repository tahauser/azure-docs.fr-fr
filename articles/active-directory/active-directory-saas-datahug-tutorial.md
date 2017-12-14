---
title: "Didacticiel : Intégration d'Azure Active Directory avec Datahug | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Datahug."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: e032aee88a430fe8ac5fa37229f1eee6fc04f710
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Didacticiel : Intégration d’Azure Active Directory avec Datahug

Dans ce didacticiel, vous allez apprendre à intégrer Datahug avec Azure Active Directory (Azure AD).

L’intégration de Datahug avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Datahug.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Datahug (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration de l’application SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Datahug, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Datahug pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Datahug depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-datahug-from-the-gallery"></a>Ajout de Datahug depuis la galerie
Pour configurer l’intégration de Datahug dans Azure AD, vous devez ajouter Datahug à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Datahug à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Datahug**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. Dans le volet de résultats, sélectionnez **Datahug**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Datahug, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Datahug correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Datahug associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** dans Datahug.

Pour configurer et tester l’authentification unique Azure AD avec Datahug, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d'un utilisateur test Datahug](#creating-a-datahug-test-user)** pour avoir un équivalent de Britta Simon dans Datahug lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Datahug.

**Pour configurer l’authentification unique Azure AD avec Datahug, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Datahug**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. Dans la section **Domaines et URL Datahug**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://apps.datahug.com/identity/<uniqueID>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://apps.datahug.com/identity/<uniqueID>/acs`

4. Cliquez sur **Afficher les paramètres d’URL avancés**. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur et l’URL de réponse. Pour obtenir ces valeurs, contactez l’[équipe de support technique Datahug](http://datahug.com/about/contact-us/). 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Vérifiez **« Afficher les paramètres avancés de signature de certificat »** et procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. Dans **Option de signature**, sélectionnez **Signer l’assertion SAML**.
    
    b. Dans **Algorithme de signature**, sélectionnez **SHA1**.
 
7. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. Dans la section **Configuration de Datahug** , cliquez sur **Configurer Datahug** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. Pour configurer l’authentification unique côté **Datahug**, vous devez envoyer le **XML de métadonnées**, **l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** téléchargés à [l’équipe de support Datahug](http://datahug.com/about/contact-us/). Ils effectuent les réglages nécessaires sur l’application pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-datahug-test-user"></a>Création d’un utilisateur de test Datahug

Pour se connecter à Datahug, les utilisateurs d’Azure AD doivent être approvisionnés dans Datahug.  
Pour Datahug, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Datahug en tant qu’administrateur.

2. Passez la souris sur la **roue dentée** dans le coin supérieur droit et cliquez sur **Paramètres**
   
   ![Ajouter un employé](./media/active-directory-saas-datahug-tutorial/1.png)

3. Choisissez **People** (Personnes) et cliquez sur l’onglet **Ajouter des utilisateurs**.

    ![Ajouter un employé](./media/active-directory-saas-datahug-tutorial/2.png)

4. Tapez l’adresse électronique de la personne pour laquelle vous souhaitez créer un compte et cliquez sur **Ajouter**.

    ![Ajouter un employé](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > Vous pouvez envoyer un courrier d’inscription à l’utilisateur en sélectionnant **Send welcome email** (Envoyer un e-mail de bienvenue).  
    > Si vous créez un compte pour l’équipe commerciale, n’envoyez pas l’e-mail de bienvenue.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Datahug.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Datahug, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Datahug**.

    ![Configurer l’authentification unique](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
Lorsque vous cliquez sur la vignette Datahug dans le volet d’accès, vous êtes automatiquement connecté à votre application Datahug. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png

