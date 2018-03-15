---
title: "Didacticiel : Intégration d’Azure Active Directory à Slack | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: jeedes
ms.openlocfilehash: cd0cecde7f98e73911e7dec734cffeeee6f09a72
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Didacticiel : Intégration d’Azure AD avec Slack

Dans ce didacticiel, vous allez apprendre à intégrer Slack à Azure Active Directory (Azure AD).

L’intégration de Slack dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Slack
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Slack (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>configuration requise

Pour configurer l’intégration d’Azure AD à Slack, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Slack pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Slack à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-slack-from-the-gallery"></a>Ajout de Slack à partir de la galerie
Pour configurer l’intégration de Slack avec Azure AD, vous devez ajouter Slack à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Slack à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **Slack**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_search.png)

5. Dans le volet de résultats, sélectionnez **Slack**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Slack avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Slack équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Slack associé doit être établie.

Dans Slack, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Slack, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Slack](#creating-a-slack-test-user)** pour avoir un équivalent de Britta Simon dans Slack, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Slack.

**Pour configurer l’authentification unique Azure AD avec Slack, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Slack**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_samlbase.png)

3. Dans la section **Domaine et URL Slack**, procédez comme suit :

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.slack.com`

    b. Dans la zone de texte **Identificateur**, tapez l’URL : `https://slack.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Pour obtenir la valeur, contactez [l’équipe de support technique Slack](https://slack.com/help/contact).
     
4. L’application Slack attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > L’adresse de messagerie est renseignée uniquement si l’utilisateur possède une **adresse de messagerie** attribuée à l’aide de l’Office 365. Dans le cas contraire, la revendication **adresse de messagerie** n’apparaît pas dans le jeton SAML.

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, sélectionnez **user.mail** en tant **qu’identificateur d’utilisateur**, et pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |  
    | User.Username | user.userprincipalname |

    a. Cliquez sur **Attribut** pour ouvrir la boîte de dialogue **Modifier l’attribut** et effectuez les opérations suivantes :

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute1.png)

    a. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    b. Dans la liste **Valeur** , sélectionnez la valeur de l’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.
    
    d. Cliquez sur **OK**

6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_certificate.png)

7. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

8. Dans la section **Configuration de Slack** , cliquez sur **Configurer Slack** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_configure.png) 

9.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Slack en tant qu’administrateur.

10.  Accédez à **Microsoft Azure AD**, puis à **Team Settings**.

     ![Configurer l’authentification unique côté application](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

11.  Dans la section **Team Settings**, cliquez sur l’onglet **Authentication** puis sur **Change Settings**.

     ![Configurer l’authentification unique côté application](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

12. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  Dans la zone de texte **Point de terminaison SAML 2.0 (HTTP)**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    b.  Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de l’**ID d’entité SAML** copié à partir du portail Azure.

    c.  Ouvrez votre fichier de certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.

    d. Configurez les trois paramètres ci-dessus comme nécessaire pour votre équipe Slack. Pour plus d’informations sur les paramètres, vous trouverez le **guide de configuration de l’authentification unique sur Slack**. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Cliquez sur **Enregistrer la configuration**.
     
    <!-- Deselect **Allow users to change their email address**.

    e.  Select **Allow users to choose their own username**.

    f.  As **Authentication for your team must be used by**, select **It’s optional**. -->

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-slack-test-user"></a>Création d’un utilisateur de test Slack

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Slack. Slack prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Slack s’il n’existe pas déjà.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support technique Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Slack.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Slack, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Slack**.

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Slack dans le volet d’accès, vous devez être connecté automatiquement à votre application Slack.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

