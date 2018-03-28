---
title: 'Tutoriel : Intégration d’Azure Active Directory à Qumu Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Qumu Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8c4a97b-4de6-49d4-b64e-42222c2ec6c9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jeedes
ms.openlocfilehash: fc4529d88d393ff18ef36415b09c0e21c97cdcf8
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-azure-active-directory-integration-with-qumu-cloud"></a>Tutoriel : Intégration d’Azure Active Directory à Qumu Cloud

Dans ce tutoriel, vous allez apprendre à intégrer Qumu Cloud à Azure Active Directory (Azure AD).

L’intégration de Qumu Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Qumu Cloud
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Qumu Cloud (authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Qumu Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Qumu Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Qumu Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-qumu-cloud-from-the-gallery"></a>Ajout de Qumu Cloud à partir de la galerie
Pour configurer l’intégration de Qumu Cloud à Azure AD, vous devez ajouter Qumu Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Qumu Cloud à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Qumu Cloud**, sélectionnez **Qumu Cloud** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Qumu Cloud dans la liste des résultats](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Qumu Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Qumu Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Qumu Cloud associé doit être établie.

Pour configurer et tester l’authentification unique avec Azure AD avec Qumu Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Qumu Cloud](#create-a-qumu-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Qumu Cloud, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Qumu Cloud.

**Pour configurer l’authentification unique Azure AD avec Qumu Cloud, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Qumu Cloud**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_samlbase.png)

3. Dans la section **Domaine et URL Qumu Cloud**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par IDP** :

    ![Informations d’authentification unique dans Domaine et URL Qumu Cloud](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.qumucloud.com/saml/SSO`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.qumucloud.com/saml/SSO`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Qumu Cloud](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.qumucloud.com`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Qumu Cloud](mailto:support@qumu.com).

4. L’application Qumu Cloud attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/active-directory-saas-qumucloud-tutorial/attribute.png)
    
5. Dans la section **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** pour développer les attributs. Dans chacun des attributs affichés, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | urn:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |

    a. Cliquez sur l’attribut pour ouvrir la fenêtre **Modifier l’attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_04.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    ![Configure Single Sign-On](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_05.png)

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez la zone de texte **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-qumucloud-tutorial/tutorial_general_400.png)
    
7. Pour configurer l’authentification unique côté **Qumu Cloud**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe de support technique de Qumu Cloud](mailto:support@qumu.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-qumu-cloud-test-user"></a>Créer un utilisateur de test Qumu Cloud

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Qumu Cloud. Qumu Cloud prend en charge le déploiement juste-à-temps, qui est l’option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Qumu Cloud s’il n’existe pas encore.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe d’assistance client Qumu Cloud](mailto:support@qumu.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Qumu Cloud.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Qumu Cloud, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Qumu Cloud**.

    ![Lien Qumu Cloud dans la liste Applications](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Qumu Cloud dans le volet d’accès, vous devez être automatiquement authentifié auprès de votre application Qumu Cloud.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_203.png

