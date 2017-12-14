---
title: "Didacticiel : Intégration d’Azure Active Directory avec Achieve3000 | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Achieve3000."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 83a83d07-ff9c-46c4-b5ba-25fe2b2cd003
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jeedes
ms.openlocfilehash: 03a6eb581dae88e9789b73c04d5424c0ed14cf62
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-achieve3000"></a>Didacticiel : Intégration d’Azure Active Directory avec Achieve3000

Ce didacticiel explique comment intégrer Achieve3000 avec Azure Active Directory (Azure AD).

L’intégration d’Achieve3000 avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Achieve3000.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Achieve3000 (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans Achieve3000, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Achieve3000 pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Achieve3000 à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-achieve3000-from-the-gallery"></a>Ajout d’Achieve3000 à partir de la galerie
Pour configurer l’intégration d’Achieve3000 dans Azure AD, vous devez ajouter Achieve3000 à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter Achieve3000 à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez **Achieve3000**, sélectionnez **Achieve3000** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Achieve3000 dans la liste des résultats](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Achieve3000, sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Achieve3000 correspondant à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Achieve3000 associé doit être établie.

Dans Achieve3000, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Achieve3000, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Achieve3000](#create-an-achieve3000-test-user)** pour avoir un équivalent de Britta Simon dans Achieve3000, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Achieve3000.

**Pour configurer l’authentification unique Azure AD avec Achieve3000, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Achieve3000**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_samlbase.png)

3. Dans la section **Domaine et URL Achieve3000**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Achieve3000](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://saml.achieve3000.com/district/<District Identifier>`

    b. Dans la zone de texte **Identificateur**, entrez la valeur : `achieve3000-saml`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Achieve3000](https://www.achieve3000.com/contact-us/). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_certificate.png) 

5. L’application Achieve3000 attend la valeur **studentID** unique dans la revendication d’identificateur du nom. Le client peut mapper la valeur correcte pour la revendication Identificateur de nom. Pour cette démo, nous avons mappé l’**user.mail**. Toutefois, en fonction de votre identificateur unique, il se peut que vous deviez mapper la valeur correcte pour celui-ci.   

    ![Configurer l’authentification unique attb](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | studentID               | user.mail |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique Add](./media/active-directory-saas-achieve3000-tutorial/tutorial_officespace_04.png)

    ![Configurer l’authentification unique Addattb](./media/active-directory-saas-achieve3000-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-achieve3000-tutorial/tutorial_general_400.png)
    
8. Pour configurer l’authentification unique côté **Achieve3000**, vous devez envoyer le fichier **XML des métadonnées** téléchargé à [l’équipe du support Achieve3000](https://www.achieve3000.com/contact-us/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-achieve3000-test-user"></a>Créer un utilisateur de test Achieve3000

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Achieve3000. Pour ajouter des utilisateurs dans la plateforme Achieve3000, collaborez avec l’[équipe du support technique Achieve3000](https://www.achieve3000.com/contact-us/). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Achieve3000.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Achieve3000, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Achieve3000**.

    ![Lien Achieve3000 dans la liste des applications](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Achieve3000 dans le panneau d’accès, vous devez être connecté automatiquement à votre application Achieve3000.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_203.png

