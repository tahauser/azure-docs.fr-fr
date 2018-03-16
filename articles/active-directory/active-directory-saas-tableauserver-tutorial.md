---
title: "Didacticiel : intégration d’Azure Active Directory à Tableau Server | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau Server."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 3b0390c8b95a46b2c134252532bef118ea4df52d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Didacticiel : Intégration d’Azure Active Directory à Tableau Server

Dans ce didacticiel, vous allez apprendre à intégrer Tableau Server avec Azure Active Directory (Azure AD).

L’intégration de Tableau Server à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Tableau Server.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Tableau Server (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Tableau Server, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Tableau Server pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Tableau Server à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Ajout de Tableau Server à partir de la galerie
Pour configurer l’intégration de Tableau Server à Azure AD, vous devez ajouter Tableau Server, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Tableau Server à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **Tableau Server**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Dans le panneau de résultats, sélectionnez **Tableau Server**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Tableau Server grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Tableau Server équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Tableau Server associé doit être établie.

Dans Tableau Server, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Tableau Server, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Tableau Server](#creating-a-tableau-server-test-user)** pour avoir un équivalent de Britta Simon dans Tableau Server qui est lié à la représentation d’utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Tableau Server.

**Pour configurer l’authentification unique Azure AD avec Tableau Server, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Tableau Server**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Dans la section **Domaine et URL Tableau Server**, procédez comme suit :

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://azure.<domain name>.link`
    
    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://azure.<domain name>.link`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas des valeurs réelles. Plus tard, vous pourrez mettre à jour les valeurs avec l’URL et l’identificateur réels à partir de la page de configuration de Tableau Server. 

4. L’application Tableau Server attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **« Attributs utilisateur »** sur la page d’intégration de l’application. La capture d’écran suivante montre un exemple identique.
    
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | username | *user.mailnickname* |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.


6. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Tableau Server en tant qu’administrateur.
   
   a. Dans Tableau Server configuration (Configuration de Tableau Server), cliquez sur l’onglet **SAML** .
  
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Cochez la case **Use SAML for single sign-on**(Utiliser SAML pour l’authentification unique).
   
   c. Tableau Server return URL (URL de retour Tableau Server) : URL à laquelle accèdent les utilisateurs Tableau Server, telle que http://tableau_server. L’utilisation de l’URL http://localhost n’est pas recommandée. L’utilisation d’une URL avec une barre oblique finale (par exemple, http://tableau_server/) n’est pas prise en charge. Copiez l’**URL de renvoi Tableau Server** et collez-la dans la zone de texte **URL de connexion** d’Azure AD, dans la section **Domaine et URL Tableau Server**.
   
   d. SAML entity ID (ID d’entité SAML) : l’ID d’entité identifie de façon unique votre installation Tableau Server auprès du fournisseur d’identité. Vous pouvez à nouveau entrer l’URL Tableau Server ici, si vous le souhaitez, mais ce n’est pas obligatoire. Copiez l’**ID d’entité SAML** et collez-la dans la zone de texte **Identificateur** d’Azure AD, dans la section **Domaine et URL Tableau Server**.
     
   e. Cliquez sur **Exporter le fichier de métadonnées** et ouvrez-le dans l’application de l’éditeur de texte. Recherchez l’URL Assertion Consumer Service avec HTTP POST et Index 0, puis copiez l’URL. Collez maintenant cette URL dans la zone de texte **URL de réponse** d’Azure AD, dans la section **Domaine et URL Tableau Server**.
   
   f. Localisez votre fichier de métadonnées de fédération téléchargé à partir du portail Azure, puis chargez-le dans le **fichier de métadonnées du fournisseur d’identité SAML**.
   
   g. Sur la page Configuration de Tableau Server, cliquez sur le bouton **OK**.
   
    >[!NOTE] 
    >Le client doit charger l’ensemble des certificats dans la configuration SAML SSO de Tableau Server. Les certificats seront ignorés dans le flux SSO.
    >Si vous avez besoin d’aide pour la configuration de SAML dans Tableau Server, consultez l’article [Configurer SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-tableau-server-test-user"></a>Création d’un utilisateur de test Tableau Server

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Tableau Server. Vous devez approvisionner tous les utilisateurs dans Tableau Server. 

Le nom de l’utilisateur doit correspondre à la valeur que vous avez configurée dans l’attribut personnalisé Azure AD **username**. Avec le mappage correct, l’intégration doit fonctionner. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Si vous avez besoin de créer un utilisateur manuellement, vous devez contacter l’administrateur Tableau Server de votre organisation.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tableau Server.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Tableau Server, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Tableau Server**.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Tableau Server dans le volet d’accès, vous devez être connecté automatiquement à votre application Tableau Server.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

