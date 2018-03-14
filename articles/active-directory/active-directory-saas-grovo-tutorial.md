---
title: "Didacticiel : Intégration d’Azure Active Directory avec Grovo | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Grovo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: e4bb050e96d3e8d9da4666f5418ac3e444f6212d
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Didacticiel : Intégration d’Azure Active Directory avec Grovo

Dans ce didacticiel, vous apprenez à intégrer Grovo avec Azure Active Directory (Azure AD).

L’intégration de Grovo avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Grovo.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Grovo (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Grovo, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Grovo pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Grovo depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-grovo-from-the-gallery"></a>Ajout de Grovo depuis la galerie
Pour configurer l’intégration de Grovo à Azure AD, vous devez ajouter Grovo, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Grovo à partir de la galerie, suivez ces étapes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Grovo**, sélectionnez **Grovo** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Grovo dans la liste des résultats](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Grovo, au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Grovo équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Grovo associé doit être établie.

Dans Grovo, affectez la valeur de **nom d’utilisateur** dans Azure AD comme étant la valeur de **Username** (Nom d’utilisateur) pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Grovo, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Grovo](#create-a-grovo-test-user)** pour obtenir dans Grovo un équivalent de Britta Simon qui soit lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD sur le portail Azure et configurez l’authentification unique dans votre application Grovo.

**Pour configurer l’authentification unique Azure AD avec Grovo, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Grovo**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_samlbase.png)

3. Dans la section **Domaines et URL Grovo**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

4. Cochez l’option **Afficher les paramètres d’URL avancés**, puis procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url1.png)

    a. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<subdomain>.grovo.com`

    b. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url2.png)
    
    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse, l’URL de connexion et l’état de relais exacts. Pour obtenir ces valeurs, contactez [l’équipe du support technique Grovo](https://www.grovo.com/contact-us).
 
5. L’application Grovo attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. Mappez l’**Identificateur d’utilisateur** avec **user.mail**, puis configurez les autres attributs tel que représenté dans la capture ci-dessous.
    
    ![Configurer l’authentification unique attb](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_attribute.png)
    
6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | Prénom          | user.givenname |
    | Nom           | user.surname |
    | Adresse de messagerie       | user.mail    |
    | employeeID          | user.employeeid |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique Add](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique Addattb](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.


7. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-grovo-tutorial/tutorial_general_400.png)

9. Dans la section **Configuration de Grovo**, cliquez sur **Configurer Grovo** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_configure.png) 

10. Dans une autre fenêtre du navigateur web, connectez-vous à Grovo en tant qu’administrateur.

11. Accédez à **Admin** > **Integrations** (Intégrations).
 
    ![Configuration de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_admin.png) 

12. Cliquez sur **SET UP** (CONFIGURER) sous la section **SP Initiated SAML 2.0** (SAML 2.0 initié SP).

    ![Configuration de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_setup.png)

13. Dans la fenêtre contextuelle **SP initiated SAML 2.0** (SAML 2.0 initié SP), effectuez les actions suivantes :

    ![Configuration de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_saml.png)

    a. Dans la zone de texte **Entity id** (ID d’entité), collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **Single sign on service endpoint** (Point de terminaison du service d’authentification unique), collez la valeur de **SAML Single Sign-On Service URL** (URL du service d’authentification unique SAML) que vous avez copiée à partir du portail Azure.

    c. Sélectionnez **Single sign on service endpoint binding** (Liaison de point de terminaison du service d’authentification unique) comme étant `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Ouvrez le **Certificat codé en base 64** téléchargé à partir du portail Azure dans le bloc-notes, collez-le dans la zone de texte **Public key** (Clé publique).

    e. Cliquez sur **Suivant**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-grovo-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-grovo-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-grovo-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-grovo-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-grovo-test-user"></a>Création d’un utilisateur de test Grovo

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Grovo. Grovo prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à Grovo.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique Grovo](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Grovo.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Grovo, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Grovo**.

    ![Lien Grovo dans la liste des applications](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Grovo dans le volet d’accès vous connecte automatiquement à votre application Grovo.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_203.png

