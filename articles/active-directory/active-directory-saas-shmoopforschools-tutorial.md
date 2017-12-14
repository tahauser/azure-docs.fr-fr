---
title: "Didacticiel : Intégration d’Azure Active Directory à Shmoop For Schools | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Shmoop For Schools."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jeedes
ms.openlocfilehash: 48db70834f96adbb7097457caca8489ea1a57da5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Didacticiel : Intégration d’Azure Active Directory à Shmoop For Schools

Dans ce didacticiel, vous allez apprendre à intégrer Shmoop For Schools à Azure Active Directory (Azure AD).

L’intégration de Shmoop For Schools à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Shmoop For Schools.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Shmoop For Schools avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Shmoop For Schools, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Shmoop For Schools avec authentification unique

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester les étapes de ce didacticiel, voici quelques recommandons :

- N’utilisez votre environnement de production que si nécessaire.
- Obtenez un [essai d’un mois](https://azure.microsoft.com/pricing/free-trial/) si vous n’avez pas déjà un environnement d’essai Azure AD.

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Shmoop For Schools à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Ajouter Shmoop For Schools à partir de la galerie
Pour configurer l’intégration de Shmoop For Schools à Azure AD, vous devez ajouter Shmoop For Schools dans votre liste d’applications SaaS managées à partir de la galerie.

**Pour ajouter Shmoop For Schools à partir de la galerie, procédez comme suit :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Shmoop For Schools**. Sélectionnez **Shmoop For Schools** dans les résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l'application.

    ![Shmoop For Schools dans la liste des résultats](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Shmoop For Schools avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Shmoop For Schools équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Shmoop For Schools.

Dans Shmoop For Schools, donnez à la valeur **Username** la même valeur que le **nom d’utilisateur** dans Azure AD. Vous avez à présent établi la relation.

Pour configurer et tester l’authentification unique Azure AD avec Shmoop For Schools, suivez les indications des sections ci-après :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Shmoop For Schools](#create-a-shmoop-for-schools-test-user) pour avoir un équivalent de Britta Simon dans Shmoop For Schools qui soit associé à la représentation de l’utilisateur Azure AD.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Shmoop For Schools.

**Pour configurer l’authentification unique Azure AD avec Shmoop For Schools, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Shmoop For Schools**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, dans le menu déroulant **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Dans la section **Domaine et URL Shmoop For Schools**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support du client Shmoop For Schools](mailto:support@shmoop.com). 
 
4. L’application Shmoop For Schools attend les assertions SAML dans un format précis. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre comment configurer les assertions :

    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools prend en charge deux rôles pour les utilisateurs : **Teacher** et **Student**. Configurez ces rôles dans Azure AD pour pouvoir affecter les rôles appropriés aux utilisateurs. Pour comprendre comment configurer des rôles dans Azure AD, consultez [Contrôle d’accès en fonction du rôle dans les applications cloud à l’aide d’Azure AD](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/).
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l'attribut de jeton SAML comme illustré dans l'image précédente.  Ensuite, effectuez les étapes suivantes :

    | Nom de l’attribut | Valeur de l’attribut |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. Pour ouvrir la boîte de dialogue **Ajouter un attribut**, cliquez sur **Ajouter un attribut**.
    
    ![Configurer l’authentification unique ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur**, sélectionnez la valeur de l’attribut pour cette ligne.

    d. Laissez la zone de texte **Espace de noms** vide.
    
    e. Sélectionnez **OK**.

6. Sélectionnez le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. Pour générer l’**URL** des métadonnées, effectuez les étapes suivantes :

    a. Sélectionnez **Inscriptions d’applications**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appregistrations.png)
   
    b. Pour ouvrir la boîte de dialogue **Points de terminaison**, sélectionnez **Points de terminaison**.  
    
    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpointicon.png)

    c. Cliquez sur le bouton Copier pour copier l’URL du document de métadonnées de fédération (**FEDERATION METADATA DOCUMENT**), puis collez-la dans le Bloc-notes.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpoint.png)
     
    d. Accédez à la page de propriétés **Shmoop For Schools**. Copiez l**'ID de l’application** à l’aide du bouton **Copier**. Collez-le dans le Bloc-notes.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appid.png)

    e. Générez **l’URL des métadonnées** en utilisant le modèle suivant : `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.   

8. Pour configurer l’authentification unique côté **Shmoop For Schools**, vous devez envoyer l’**URL des métadonnées** à l’[équipe de support de Shmoop For Schools](mailto:support@shmoop.com).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée en consultant la [documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**. Puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Créer un utilisateur de test Shmoop For Schools

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Shmoop For Schools. Shmoop For Schools prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n'existe pas déjà, il est créé lors de la tentative d’accès à Shmoop For Schools.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support de Shmoop For Schools](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Shmoop For Schools.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Shmoop For Schools, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications. Accédez ensuite à **Applications d’entreprise** dans la vue d'annuaire.  Ensuite, sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Shmoop For Schools**.

    ![Lien Shmoop For Schools dans la liste des applications](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**. 

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous sélectionnez la vignette **Shmoop For Schools** dans le volet d’accès, vous devez être connecté automatiquement à votre application Shmoop For Schools.

Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png

