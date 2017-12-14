---
title: "Didacticiel : Intégration d’Azure Active Directory à AirWatch | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et AirWatch."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a0e314aaf59a70107058829ea84dde362f097274
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Didacticiel : Intégration d’Azure Active Directory à AirWatch

Dans ce didacticiel, vous allez apprendre à intégrer AirWatch dans Azure Active Directory (Azure AD).

L’intégration d’AirWatch dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à AirWatch
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à AirWatch (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes depuis un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans AirWatch, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement AirWatch pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’AirWatch à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-airwatch-from-the-gallery"></a>Ajout d’AirWatch à partir de la galerie
Pour configurer l’intégration d’AirWatch dans Azure AD, vous devez ajouter AirWatch à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter AirWatch à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **AirWatch**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. Dans le volet de résultats, sélectionnez **AirWatch**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec AirWatch, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur AirWatch correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur AirWatch associé doit être établie.

Pour cela, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans AirWatch.

Pour configurer et tester l’authentification unique Azure AD avec AirWatch, vous devez vous conformer aux indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test AirWatch](#creating-a-airwatch-test-user)** pour avoir un équivalent de Britta Simon dans AirWatch lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application AirWatch.

**Pour configurer l’authentification unique Azure AD avec AirWatch, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **AirWatch**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Dans la section **Domaine et URL AirWatch**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Dans la zone de texte **Identificateur**, entrez la valeur `AirWatch`.

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe de support technique AirWatch](http://www.air-watch.com/company/contact-us/) pour obtenir cette valeur. 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Dans la section **Configuration d’AirWatch** , cliquez sur **Configurer AirWatch** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AirWatch en tant qu’administrateur.

8. Dans le volet de navigation gauche, cliquez sur **Accounts**, puis sur **Administrators**.
   
   ![Administrateurs](./media/active-directory-saas-airwatch-tutorial/ic791920.png "Administrateurs")

9. Développez le menu **Settings**, puis cliquez sur **Directory Services**.
   
   ![Paramètres](./media/active-directory-saas-airwatch-tutorial/ic791921.png "Paramètres")

10. Cliquez sur l’onglet **User** (Utilisateur) puis, dans la zone de texte **Base DN** (Nom unique de base), tapez votre nom de domaine et cliquez sur **Save** (Enregistrer).
   
   ![Utilisateur](./media/active-directory-saas-airwatch-tutorial/ic791922.png "Utilisateur")

11. Cliquez sur l’onglet **Server** .
   
   ![Serveur](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Serveur")

12. Procédez comme suit :
    
    ![Télécharger](./media/active-directory-saas-airwatch-tutorial/ic791924.png "Télécharger")   
    
    a. Pour **Directory Type**, sélectionnez **None**.

    b. Sélectionnez **Use SAML For Authentication**.

    c. Pour charger le certificat téléchargé, cliquez sur **Upload**.

13. Dans la section **Request** , procédez comme suit :
    
    ![Demande](./media/active-directory-saas-airwatch-tutorial/ic791925.png "Demande")  

    a. Pour **Request Binding Type**, sélectionnez **POST**.

    b. Dans la boîte de dialogue **Configurer l’authentification unique sur AirWatch** du portail Azure, copiez la valeur **URL du service d’authentification unique SAML**, puis collez-la dans la zone de texte **Identity Provider Single Sign On URL** (URL d’authentification unique du fournisseur d’identité).

    c. Pour **NameID Format**, sélectionnez **Email Address**.

    d. Cliquez sur **Save**.

14. Cliquez à nouveau sur l’onglet **User** .
    
    ![Utilisateur](./media/active-directory-saas-airwatch-tutorial/ic791926.png "Utilisateur")

15. Dans la section **Attribute** , procédez comme suit :
    
    ![Attribut](./media/active-directory-saas-airwatch-tutorial/ic791927.png "Attribut")

    a. Dans la zone de texte **Object Identifier**, tapez **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. Dans la zone de texte **Username**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. Dans la zone de texte **Display Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. Dans la zone de texte **First Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Dans la zone de texte **Last Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. Dans la zone de texte **Email**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Cliquez sur **Save**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur** , tapez l’**adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-airwatch-test-user"></a>Création d’un utilisateur de test AirWatch

Pour se connecter à AirWatch, les utilisateurs d’Azure AD doivent être approvisionnés dans AirWatch.

* Dans le cas d’AirWatch, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **AirWatch** en tant qu’administrateur.
2. Dans le volet de navigation situé sur le côté gauche, cliquez sur **Accounts**, puis sur **Users**.
   
   ![Utilisateurs](./media/active-directory-saas-airwatch-tutorial/ic791929.png "Utilisateurs")
3. Dans le menu **Users**, cliquez sur **List View**, puis sur **Add \> Add User**.
   
   ![Ajouter un utilisateur](./media/active-directory-saas-airwatch-tutorial/ic791930.png "Ajouter un utilisateur")
4. Dans la boîte de dialogue **Add / Edit User** , procédez comme suit :

   ![Ajouter un utilisateur](./media/active-directory-saas-airwatch-tutorial/ic791931.png "Ajouter un utilisateur")   
   1. Tapez le nom d’utilisateur, le mot de passe, la confirmation du mot de passe, le prénom, le nom et l’adresse e-mail du compte Azure Active Directory valide que vous souhaitez approvisionner dans les champs correspondants, à savoir **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** et **Email Address**.
   2. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur AirWatch fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AirWatch.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à AirWatch, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **AirWatch**.

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

