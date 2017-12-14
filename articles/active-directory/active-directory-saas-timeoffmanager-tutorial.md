---
title: "Didacticiel : Intégration d’Azure Active Directory à TimeOffManager | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et TimeOffManager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 5638a1b2271fe50344c29de1e3883ca67c20c9ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Didacticiel : Intégration d’Azure AD à TimeOffManager

Dans ce didacticiel, vous allez apprendre à intégrer TimeOffManager dans Azure Active Directory (Azure AD).

L’intégration de TimeOffManager dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TimeOffManager
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à TimeOffManager (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans TimeOffManager, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TimeOffManager pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter TimeOffManager à partir de la galerie
2. Configurer et tester l’authentification unique Azure AD

## <a name="add-timeoffmanager-from-the-gallery"></a>Ajouter TimeOffManager à partir de la galerie
Pour configurer l’intégration de TimeOffManager avec Azure AD, vous devez ajouter TimeOffManager, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter TimeOffManager à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **TimeOffManager**, sélectionnez **TimeOffManager** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Ajouter à partir de la galerie](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TimeOffManager, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TimeOffManager équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur TimeOffManager associé doit être établie.

Dans TimeOffManager, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec TimeOffManager, vous devez vous conformer aux indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test TimeOffManager](#create-a-timeoffmanager-test-user)** pour avoir un équivalent de Britta Simon dans TimeOffManager lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application TimeOffManager.

**Pour configurer l’authentification unique Azure AD avec TimeOffManager, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **TimeOffManager**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Authentification basée sur SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. Dans la section **Domaine et URL TimeOffManager**, procédez comme suit :

     ![Section Domaine et URL TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Vous pouvez obtenir cette valeur à partir de la **page des paramètres d’authentification unique**, décrite plus loin dans le didacticiel, ou contacter l’[équipe de support technique TimeOffManager](http://www.timeoffmanager.com/contact-us.aspx).
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Section Certificat de signature SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. Cette section explique comment permettre aux utilisateurs de s’authentifier sur TimeOffManager avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.
    
    Votre application TimeOffManager s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre un exemple :

    ![saml token attributes](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "saml token attributes")
    
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |
    | Firstname |User.givenname |
    | Lastname |User.Surname |
    | Email |User.mail |
    
    a.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **ajouter un attribut utilisateur**.
    
    ![saml token attributes](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "saml token attributes")
    
    ![saml token attributes](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "saml token attributes")
    
    b.  Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.
    
    c.  Dans la zone de texte **Valeur de l’attribut**, sélectionnez la valeur d’attribut indiquée pour cette ligne.
    
    d.  Cliquez sur **OK**.
    
6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de TimeOffManager**, cliquez sur **Configurer TimeOffManager** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Section Configuration de TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TimeOffManager en tant qu’administrateur.

9. Accédez à **Account \> Account Options \> Single Sign-On Settings**.
   
   ![Paramètres d’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/ic795917.png "paramètres d’authentification unique")
7. Dans la section **Single Sign-On Settings** , procédez comme suit :
   
   ![Paramètres d’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/ic795918.png "paramètres d’authentification unique")
   
   a. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .
   
   b. Dans la zone de texte **Émetteur IdP**, collez la valeur de **ID d’entité SAML** que vous avez copiée à partir du portail Azure.
   
   c. Dans la zone de texte **IdP Endpoint URL** (URL du point de terminaison IdP), collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.
   
   d. Dans **Enforce SAML**, sélectionnez **No**.
   
   e. Dans **Auto-Create Users**, sélectionnez **Yes**.
   
   f. Dans la zone de texte **Logout URL (URL de déconnexion)**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.
   
   g. Cliquez sur **Enregistrer les modifications**.

11. Sur la page **Paramètres de l’authentification unique**, copiez la valeur **URL Assertion Consumer Service**  et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL TimeOffManager** dans le portail Azure. 

      ![Paramètres d’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/ic795915.png "paramètres d’authentification unique")

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Utilisateurs et groupes --> Tous les utilisateurs](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Bouton Ajouter](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue utilisateur](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Créer un utilisateur de test TimeOffManager

Pour se connecter à TimeOffManager, les utilisateurs d’Azure AD doivent être approvisionnés dans TimeOffManager.  

TimeOffManager prend en charge l’approvisionnement juste-à-temps des utilisateurs. Vous n’avez rien à faire.  

Les utilisateurs sont ajoutés automatiquement lors de la première connexion à l’aide de l’authentification unique.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par TimeOffManager, pour approvisionner des comptes d’utilisateur Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TimeOffManager.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à TimeOffManager, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **TimeOffManager**.

    ![TimeOffManager dans la liste des applications](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette TimeOffManager dans le panneau d’accès, vous devez être connecté automatiquement à votre application TimeOffManager. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_203.png

