---
title: "Didacticiel : Intégration d’Azure Active Directory à ServiceChannel | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et ServiceChannel."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: b744b8e197c918e7fe3a0e6e7eeab6a70cc6e34a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Didacticiel : Intégration d’Azure Active Directory à ServiceChannel

Dans ce didacticiel, vous découvrez comment intégrer ServiceChannel à Azure Active Directory (Azure AD).

L’intégration de ServiceChannel à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ServiceChannel.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ServiceChannel (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ServiceChannel, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ServiceChannel pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ServiceChannel à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-servicechannel-from-the-gallery"></a>Ajout de ServiceChannel à partir de la galerie
Pour configurer l’intégration de ServiceChannel à Azure AD, vous devez ajouter ServiceChannel à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter ServiceChannel à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **ServiceChannel**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_000.png)

5. Dans le volet des résultats, sélectionnez **ServiceChannel** puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec ServiceChannel avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ServiceChannel équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur ServiceChannel associé.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans ServiceChannel.

Pour configurer et tester l’authentification unique Azure AD avec ServiceChannel, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test ServiceChannel](#creating-a-servicechannel-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail de gestion Azure et vous configurez l’authentification unique dans votre application ServiceChannel.

**Pour configurer l’authentification unique Azure AD avec ServiceChannel, procédez comme suit :**

1. Dans le portail de gestion Azure, dans la page d’intégration de l’application **ServiceChannel**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_01.png)

3. Dans la section **Domaine et URL ServiceChannel**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `http://adfs.<domain>.com/adfs/service/trust`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Contactez l’[équipe de support technique de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) pour obtenir ces valeurs.

4. Votre application ServiceChannel attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre un exemple : **NameIdentifier (Identifiant utilisateur)** est la seule revendication obligatoire et la valeur par défaut est **user.userprincipalname**, mais ServiceChannel attend que ceci soit mappé à **user.mail**. Si vous prévoyez d’activer l’approvisionnement des utilisateurs juste à temps, vous devez ajouter les revendications suivantes comme indiqué ci-dessous. La revendication **Rôle** doit être mappée à **user.assignedroles**, qui contient le rôle de l’utilisateur.  

    Vous pouvez consulter le guide de ServiceChannel [ici](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) pour obtenir des instructions sur les revendications.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Cliquez [ici](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/) pour savoir comment configurer un **rôle** dans Azure AD.

5. Dans **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** et définissez les attributs.

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | Rôle| user.assignedroles |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.
    
6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_05.png) 

7. Cliquez sur **Save**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial_general_400.png)

8. Dans la section **Configuration de ServiceChannel**, cliquez sur **Configurer ServiceChannel** pour ouvrir la fenêtre **Configurer l’authentification**. Notez l’**ID d’entité SAML** dans la section **Référence rapide**.

9. Pour configurer l’authentification unique du côté **ServiceChannel**, vous devez envoyer le **certificat (Base64)** téléchargé et **ID d’entité SAML** à l’[équipe de support technique de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us). Ils effectueront les réglages nécessaires pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 

### <a name="creating-a-servicechannel-test-user"></a>Création d’un utilisateur de test ServiceChannel

L’application prend en charge la configuration d’utilisateur juste-à-temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. Pour l’approvisionnement de l’utilisateur complet, contactez l’[équipe de support technique de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous permettez à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à ServiceChannel.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à ServiceChannel, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ServiceChannel**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_app01.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ServiceChannel dans le volet d’accès, vous êtes normalement connecté automatiquement à votre application ServiceChannel.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_203.png