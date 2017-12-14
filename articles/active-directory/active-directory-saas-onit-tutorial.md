---
title: "Didacticiel : Intégration d’Azure Active Directory à Onit | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Onit."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: f001d2f9d5efd2b9252f0affc32506bd615437af
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Didacticiel : Intégration d’Azure Active Directory avec Onit

Dans ce didacticiel, vous allez apprendre à intégrer Onit à Azure Active Directory (Azure AD).

L’intégration d’Onit dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Onit.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Onit (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Onit, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Onit pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Onit depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-onit-from-the-gallery"></a>Ajout d’Onit depuis la galerie
Pour configurer l’intégration d’Onit avec Azure AD, vous devez ajouter Onit, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Onit à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Onit**, sélectionnez **Onit** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Onit dans la liste des résultats](./media/active-directory-saas-onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Onit, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Onit correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Onit associé doit être établie.

Dans Onit, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Onit, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Onit](#create-an-onit-test-user)** pour avoir un équivalent de Britta Simon dans Onit lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Onit.

**Pour configurer l’authentification unique Azure AD avec Onit, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Onit**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-onit-tutorial/tutorial_onit_samlbase.png)

3. Dans la section **Domaine et URL Onit**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<sub-domain>.onit.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Onit](https://www.onit.com/support). 
 
4. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT** du certificat.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-onit-tutorial/tutorial_onit_certificate.png) 

5. L’application Onit attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attribut** de l’application. La capture d’écran suivante montre un exemple : 

    ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/tutorial_onit_attribute.png) 

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |
    | email | user.mail |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/tutorial_general_400.png)

8. Dans la section **Configuration d’Onit** , cliquez sur **Configurer Onit** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration d’Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_configure.png)

9. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Onit en tant qu’administrateur.

10. Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")
11. Cliquez sur **Edit Corporation**.
   
   ![Modifier l’entreprise](./media/active-directory-saas-onit-tutorial/IC791175.png "Modifier l’entreprise")
   
12. Cliquez sur l’onglet **Security** .
    
    ![Modifier les informations de l’entreprise](./media/active-directory-saas-onit-tutorial/IC791176.png "Modifier les informations de l’entreprise")

13. Sous l’onglet **Security** , procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-onit-tutorial/IC791177.png "Authentification unique")

    a. Comme **Authentication Strategy**, sélectionnez **Single Sign On and Password**.
    
    b. Dans la zone de texte **URL cible du fournisseur d’identité**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **URL de déconnexion de fournisseur d’identité**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Empreinte du certificat de fournisseur d’identité (SHA1)**, collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-onit-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-onit-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-onit-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-onit-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-an-onit-test-user"></a>Création d’un utilisateur de test Onit

Pour se connecter à Onit, les utilisateurs d’Azure AD doivent être approvisionnés dans Onit.  

Dans le cas d’Onit, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous au site d’entreprise **Onit** en tant qu’administrateur.
2. Cliquez sur **Add User**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")
3. Dans la boîte de dialogue **Add user** , procédez comme suit :
   
   ![Ajouter un utilisateur](./media/active-directory-saas-onit-tutorial/IC791181.png "Ajouter un utilisateur")
   
  1. Tapez le **nom** et l’**adresse électronique** d’un compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
  2. Cliquez sur **Create**.    
   
 > [!NOTE]
 > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Onit.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Onit, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Onit**.

    ![Lien Onit dans la liste des applications](./media/active-directory-saas-onit-tutorial/tutorial_onit_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Onit dans le volet d’accès, vous devez être connecté automatiquement à votre application Onit.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onit-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onit-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onit-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onit-tutorial/tutorial_general_203.png

