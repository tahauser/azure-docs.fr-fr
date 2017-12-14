---
title: "Didacticiel : Intégration d’Azure Active Directory avec LinkedIn Sales Navigator | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Sales Navigator."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: fde1d2c5d1a90092ec4528f727dc7af672f80917
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Didacticiel : Intégration d’Azure Active Directory avec LinkedIn Sales Navigator

Dans ce didacticiel, vous allez apprendre à intégrer LinkedIn Sales Navigator dans Azure Active Directory (Azure AD).

L’intégration de LinkedIn Sales Navigator dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LinkedIn Sales Navigator
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LinkedIn Sales Navigator (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans LinkedIn Sales Navigator, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LinkedIn Sales Navigator pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Évitez d’utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LinkedIn Sales Navigator à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Ajout de LinkedIn Sales Navigator à partir de la galerie
Pour configurer l’intégration de LinkedIn Sales Navigator avec Azure AD, vous devez ajouter LinkedIn Sales Navigator à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter LinkedIn Sales Navigator à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **LinkedIn Sales Navigator**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. Dans le volet des résultats, sélectionnez **LinkedIn Sales Navigator**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LinkedIn Sales Navigator par le biais d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LinkedIn Sales Navigator équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur LinkedIn Sales Navigator associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans LinkedIn Sales Navigator.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Sales Navigator, vous devez vous conformer aux instructions des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LinkedIn Sales Navigator](#creating-a-linkedin-sales-navigator-test-user)** pour avoir un équivalent de Britta Simon dans LinkedIn Sales Navigator lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application LinkedIn Sales Navigator.

**Pour configurer l’authentification unique Azure AD avec LinkedIn Sales Navigator, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **LinkedIn Sales Navigator**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** afin d’activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. Dans une autre fenêtre de navigateur web, connectez-vous à votre site web **LinkedIn Sales Navigator** en tant qu’administrateur.

4. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. En outre, sélectionnez **Sales Navigator** dans la liste déroulante.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. Cliquez sur **OU cliquez ici pour charger et copier des champs du formulaire** et copiez **ID d’entité** et **URL ACS**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. Dans la section **Domaines et URL LinkedIn Sales Navigator** du portail Azure, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. Dans la zone de texte **Identificateur**, entrez **l’ID d’entité** copié à partir de LinkedIn Portal 

    b. Dans la zone de texte **URL de réponse**, entrez l’**URL ACS** copiée à partir du portail LinkedIn

7. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

8. Votre application **LinkedIn Sales Navigator** attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante présente un exemple. La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais LinkedIn Sales Navigator s’attend à ce qu’elle soit mappée sur l’adresse de messagerie de l’utilisateur. Vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation. 

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. Dans **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** et définissez les attributs. L’utilisateur doit ajouter quatre revendications nommées **email**, **department**, **firstname** et **lastname** et la valeur doit être mappée respectivement à **user.mail**, **user.department**, **user.givenname** et **user.surname**.

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue associée.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

10. Effectuez les étapes suivantes au niveau de l’attribut **name**.

    a. Cliquez sur l’attribut pour ouvrir la fenêtre **Modifier l’attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/url_update.png)

    b. Supprimez la valeur d’URL dans **namespace**.
    
    c. Cliquez sur **OK** pour enregistrer le paramètre.

11. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Cliquez sur **Télécharger fichier XML** pour charger le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passe de **Non connecté** à **Connecté**

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. Allez dans **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Création d’un utilisateur de test LinkedIn Sales Navigator

L’application LinkedIn Sales Navigator prend en charge la configuration d’utilisateur juste à temps (JIT), et après authentification, les utilisateurs sont créés automatiquement dans l’application. Activez **Affecter automatiquement les licences** pour affecter une licence à l’utilisateur.
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Sales Navigator.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LinkedIn Sales Navigator, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LinkedIn Sales Navigator**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette LinkedIn Sales Navigator dans le volet d’accès, vous êtes normalement redirigé vers page d’organisation où vous devez fournir vos informations de compte personnelles LinkedIn. Votre compte personnel est ainsi lié à votre compte professionnel LinkedIn. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_203.png

