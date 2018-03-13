---
title: "Didacticiel : Intégration d’Azure Active Directory à EBSCO | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et EBSCO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: ea7fe09c31d88cf2095b3a3777b6b1f9feb8df46
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Didacticiel : Intégration d’Azure Active Directory à EBSCO

Dans ce didacticiel, vous allez apprendre à intégrer EBSCO à Azure Active Directory (Azure AD).

L’intégration d’EBSCO à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à EBSCO.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à EBSCO (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à EBSCO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement EBSCO pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’EBSCO depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ebsco-from-the-gallery"></a>Ajout d’EBSCO depuis la galerie
Pour configurer l’intégration d’EBSCO à Azure AD, vous devez ajouter EBSCO, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter EBSCO à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **EBSCO**, sélectionnez **EBSCO** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![EBSCO dans la liste des résultats](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec EBSCO, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur EBSCO correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur EBSCO associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec EBSCO, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test EBSCO](#create-an-ebsco-test-user)** : vous pouvez automatiser la personnalisation et l’attribution d’utilisateurs d’EBSCOhost. EBSCO prend en charge l’attribution d’utilisateurs juste-à-temps.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application EBSCO.

**Pour configurer l’authentification unique Azure AD avec EBSCO, effectuez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **EBSCO**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. Dans la section **Domaines et URL EBSCO**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    Dans la zone de texte **Identificateur**, tapez une URL : `pingsso.ebscohost.com`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique EBSCO](mailto:sso@ebsco.com). 

    o **Éléments uniques :**  

    o **Custid** = Entrez l’ID client EBSCO unique 

    o **Profile** = Les clients peuvent adapter le lien pour diriger les utilisateurs vers un profil spécifique (selon ce qu’ils achètent auprès d’EBSCO). Ils peuvent entrer un ID de profil spécifique. Les principaux ID sont eds (service de découverte EBSCO) et ehost (bases de données EBSOCOhost). Vous trouverez des instructions les concernant [ici](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. L’application EBSCO attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > L’attribut **name** est obligatoire et il est mappé avec **User Identifier** (Identificateur d’utilisateur) dans l’application EBSCO. C’est ajouté par défaut pour vous éviter d’avoir à le faire manuellement.
    
6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. Pour configurer l’authentification unique côté **EBSCO**, vous devez envoyer le fichier **XML des métadonnées** téléchargé à [l’équipe de support technique d’EBSCO](mailto:sso@ebsco.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-ebsco-test-user"></a>Créer un utilisateur de test EBSCO

Dans le cas d’EBSCO, l’attribution d’utilisateurs est automatique.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

Azure AD transmet les données requises à l’application EBSCO. L’attribution d’utilisateurs d’EBSCO peut être automatique ou exiger un formulaire à usage unique. Cela dépend si le client a un grand nombre de comptes EBSCOhost préexistants avec des paramètres personnels enregistrés. Vous pouvez en discuter avec [l’équipe de support technique d’EBSCO](mailto:sso@ebsco.com) pendant l’implémentation. Dans les deux cas, le client n’a pas besoin de créer de comptes EBSCOhost avant de procéder aux tests.

   >[!Note]
   >Vous pouvez automatiser la personnalisation et l’attribution d’utilisateurs d’EBSCOhost. Contactez [l’équipe de support technique d’EBSCO](mailto:sso@ebsco.com) concernant l’attribution d’utilisateurs juste-à-temps. 
 
### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EBSCO.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à EBSCO, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **EBSCO**.

    ![Lien EBSCO dans la liste des applications](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

1. Quand vous cliquez sur la vignette EBSCO dans le volet d’accès, vous devez être connecté automatiquement à votre application EBSCO.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

2. Une fois connecté à l’application, cliquez sur le bouton **Sign In** (Se connecter) dans l’angle supérieur droit.

    ![Bouton Sign In (Se connecter) à EBSCO dans la liste des applications](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Vous recevez une invite à usage unique pour lier la connexion institutionnelle/SAML en choisissant l’option **Link your existing MyEBSCOhost account to your institution account now** (Lier maintenant votre compte MyEBSCOhost existant à votre compte institutionnel) ou **Create a new MyEBSCOhost account and link it to your institution account** (Créer un compte MyEBSCOhost et le lier à votre compte institutionnel). Le compte est utilisé pour la personnalisation de l’application EBSCOhost. Sélectionnez l’option de **création de compte** ; vous verrez alors que le formulaire pour la personnalisation est prérempli avec les valeurs de la réponse saml, comme indiqué dans la capture d’écran ci-dessous. Cliquez sur **Continue** (Continuer) pour enregistrer cette sélection.
    
     ![Utilisateur EBSCO dans la liste des applications](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. Après avoir effectué la configuration ci-dessus, effacez les cookies et le cache, puis reconnectez-vous. Vous ne devrez pas vous reconnecter manuellement et les paramètres de personnalisation sont mémorisés.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png

