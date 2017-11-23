---
title: "Didacticiel : intégration d’Azure Active Directory avec Apex Portal | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Apex Portal."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db204a46-6460-4ace-bdbb-4353846723ad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: jeedes
ms.openlocfilehash: 69e918f3f8826d7e79f405320676f542e346af7c
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-apex-portal"></a>Didacticiel : Intégration d’Azure Active Directory avec Apex Portal

Dans ce didacticiel, vous allez apprendre à intégrer Apex Portal à Azure Active Directory (Azure AD).

L’intégration d’Apex Portal avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Apex Portal.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Apex Portal (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Apex Portal, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Apex Portal pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Apex Portal à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-apex-portal-from-the-gallery"></a>Ajout d’Apex Portal à partir de la galerie
Pour configurer l’intégration d’Apex Portal à Azure AD, vous devez ajouter Apex Portal depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Apex Portal à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Apex Portal**, sélectionnez **Apex Portal** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Apex Portal dans la liste des résultats](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Apex Portal, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Apex Portal équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Apex Portal associé doit être établie.

Dans Apex Portal, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Apex Portal, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Apex Portal](#create-an-apex-online-test-user)** pour avoir un équivalent de Britta Simon dans Apex Portal, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Apex Portal.

**Pour configurer l’authentification unique Azure AD avec Apex Portal, effectuez les étapes suivantes :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Apex Portal**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_samlbase.png)

3. Dans la section **Domaine et URL Apex Portal**, effectuez les étapes suivantes :

    ![Informations d’authentification unique Domaine et URL Apex Portal](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support Apex Portal](mailto:support@apexanalytix.com).
 
4. Votre application Apex Portal attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. 

    ![Configurer l’authentification unique](./media/active-directory-saas-apexportal-tutorial/attribute.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | FIRSTNAME | user.givenname |
    | LASTNAME | user.surname |
    | MAIL | user.mail |    

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-apexportal-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-apexportal-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-apexportal-tutorial/tutorial_general_400.png)

8. Pour configurer l’authentification unique côté **Apex Portal**, vous devez envoyer le fichier **XML des métadonnées** téléchargé à [l’équipe de support technique Apex Portal](mailto:support@apexanalytix.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-apexportal-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-apexportal-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-apexportal-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-apexportal-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-an-apex-portal-test-user"></a>Créer un utilisateur de test Apex Portal

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Apex Portal. Apex Portal prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé durant une tentative d’accès à Apex Portal.
 
> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Apex Portal](mailto:support@apexanalytix.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Apex Portal.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Apex Portal, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Apex Portal**.

    ![Lien Apex Portal dans la liste des applications](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Apex Portal dans le Panneau d’accès, vous devez être connecté automatiquement à votre application Apex Portal.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_203.png

