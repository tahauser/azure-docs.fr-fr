---
title: 'Didacticiel : Intégration d’Azure Active Directory à SAP Cloud Platform | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: a578a732efa3d2b91240ef0f4c02670aea8e547f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Didacticiel : Intégration d’Azure Active Directory avec SAP Cloud Platform

Dans ce didacticiel, vous allez apprendre à intégrer SAP Cloud Platform à Azure Active Directory (Azure AD).

L’intégration de SAP Cloud Platform à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SAP Cloud Platform.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SAP Cloud Platform (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à SAP Cloud Platform, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP Cloud Platform pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SAP Cloud Platform pourront s’authentifier de manière unique dans l’application (connexion initiée par le fournisseur du service) à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Vous devez déployer votre propre application ou vous abonner à une application sur votre compte SAP Cloud Platform pour tester l’authentification unique. Dans ce didacticiel, une application est déployée dans le compte.
> 

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP Cloud Platform à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Ajout de SAP Cloud Platform à partir de la galerie
Pour configurer l’intégration de SAP Cloud Platform à Azure AD, vous devez ajouter SAP Cloud Platform disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Cloud Platform à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SAP Cloud Platform**, sélectionnez **SAP Cloud Platform** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SAP Cloud Platform dans la liste des résultats](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur SAP Cloud Platform correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur SAP Cloud Platform associé.

Dans SAP Cloud Platform, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur test SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)** pour avoir un équivalent de Britta Simon dans SAP Cloud Platform lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SAP Cloud Platform.

**Pour configurer l’authentification unique Azure AD avec SAP Cloud Platform, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SAP Cloud Platform**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Dans la section **Domaine et URL SAP Cloud Platform**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application **SAP Cloud Platform**. Il s’agit de l’URL spécifique au compte d’une ressource protégée de votre application SAP Cloud Platform. L’URL est basée sur le modèle suivant : `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Il s’agit de l’URL de votre application SAP Cloud Platform sur laquelle l’utilisateur doit s’authentifier.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Dans la zone de texte **Identificateur**, vous indiquez le type de votre SAP Cloud Platform. Tapez une URL selon l’un des modèles suivants : 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Contactez l’[équipe de support client SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) pour obtenir l’URL de connexion et l’identificateur. URL de réponse que vous obtenez dans la section Gestion des approbations qui est expliquée plus loin dans le didacticiel.
    > 
     
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre de navigateur web, connectez-vous à SAP Cloud Platform Cockpit à l’adresse `https://account.<landscape host>.ondemand.com/cockpit`(par exemple : https://account.hanatrial.ondemand.com/cockpit).

7. Cliquez sur l’onglet **Trust** .
   
    ![Trust](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "Trust")

8. Dans la section Gestion des approbations, sous **Fournisseur de services local**, procédez comme suit :

    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "Trust Management")
   
    a. Cliquez sur **Modifier**.

    b. Comme **Configuration Type**, sélectionnez **Custom**.

    c. Pour **Local Provider Name**, laissez la valeur par défaut. Copiez cette valeur et collez-la dans le champ **Identificateur** dans la configuration d’Azure AD pour SAP Cloud Platform.

    d. Pour générer une paire de clés **Signature Key** et **Signing Certificate**, cliquez sur **Generate Key Pair**.

    e. Pour **Principal Propagation**, sélectionnez **Disabled**.

    f. Pour **Force Authentication**, sélectionnez **Disabled**.

    g. Cliquez sur **Enregistrer**.

9. Une fois les paramètres **Fournisseur de services local** enregistrés, procédez comme suit pour obtenir l’URL de réponse :
   
    ![Obtenir les métadonnées](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "Obtenir les métadonnées")

    a. Téléchargez le fichier de métadonnées SAP Cloud Platform en cliquant sur **Obtenir les métadonnées**.

    b. Ouvrez le fichier XML de métadonnées SAP Cloud Platform téléchargé et recherchez la balise **ns3:AssertionConsumerService** .
 
    c. Copiez la valeur de l’attribut **Emplacement** et collez-la dans le champ **URL de réponse** dans la configuration d’Azure AD pour SAP Cloud Platform.

10. Cliquez sur l’onglet **Trusted Identity Provider**, puis sur **Add Trusted Identity Provider**.
   
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "Trust Management")
   
    >[!NOTE]
    >Pour gérer la liste de fournisseurs d’identité approuvés, vous devrez avoir choisi le type de configuration Custom dans la section Local Service Provider. Pour le type de configuration Default, vous disposez d’une approbation non modifiable et implicite au SAP ID Service. Pour None, vous n’avez aucun paramètre d’approbation.
    > 
    > 

11. Cliquez sur l’onglet **General**, puis sur **Browse** pour charger le fichier de métadonnées que vous avez téléchargé.
    
    ![Trust Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "Trust Management")
    
    >[!NOTE]
    >Après avoir téléchargé le fichier de métadonnées, les valeurs de **Authentification unique**, **URL de déconnexion unique** et de **Certificat de signature** sont remplies automatiquement.
    > 
     
12. Cliquez sur onglet **Attributes** .

13. Sous l’onglet **Attributes**, procédez comme suit :
    
    ![Attributs](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "Attributs") 

    a. Cliquez sur **Add Assertion-Based Attribute**, puis ajoutez les attributs basés sur une assertion suivants :
       
    | Assertion Attribute | Principal Attribute |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >La configuration des attributs dépend de la façon dont sont développées les applications sur SCP, en l’occurrence des attributs qu’elles attendent dans la réponse SAML et avec quel nom (Principal Attribute) elles accèdent à cet attribut dans le code.
     > 
    
    b. L’attribut **Default Attribute** de la capture d’écran ne sert qu’à des fins d’illustration. Il n’est pas nécessaire de faire fonctionner le scénario.  
 
    c. Les noms et valeurs de **Principal Attribute** dans la capture d’écran dépendent de la façon dont l’application est développée. Il est possible que votre application exige des mappages différents.

### <a name="assertion-based-groups"></a>Groupes basés sur une assertion

Une étape facultative est de configurer des groupes basés sur une assertion pour votre fournisseur d’identité Azure Active Directory.

L’utilisation de SAP Cloud Platform vous permet d’attribuer de manière dynamique un ou plusieurs utilisateurs à un ou plusieurs rôles dans vos applications SAP Cloud Platform, en fonction des valeurs des attributs de l’assertion SAML 2.0. 

Par exemple, si l’assertion contient l’attribut « *contract=temporaire* », vous souhaiterez peut-être que tous les utilisateurs affectés soient ajoutés au groupe « *TEMPORAIRE* ». Le groupe « *TEMPORAIRE* » peut contenir un ou plusieurs rôles d’une ou plusieurs applications déployées dans votre compte SAP Cloud Platform.
 
Utilisez des groupes basés sur une assertion quand vous voulez affecter simultanément plusieurs utilisateurs à un ou plusieurs rôles d’applications dans votre compte SAP Cloud Platform. Si vous ne voulez affecter qu’un seul utilisateur ou un petit nombre d’utilisateurs à des rôles spécifiques, nous vous recommandons de les affecter directement dans l’onglet « **Autorisations** » de SAP Cloud Platform Cockpit.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Créer un utilisateur test SAP Cloud Platform

Pour permettre aux utilisateurs d’Azure AD de se connecter à SAP Cloud Platform, vous devez leur attribuer des rôles dans SAP Cloud Platform.

**Pour affecter un rôle à un utilisateur, procédez comme suit :**

1. Connectez-vous à votre cockpit **SAP Cloud Platform**.

2. Procédez comme suit :
   
    ![Autorisations](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "Autorisations")
   
    a. Cliquez sur **Authorization**.

    b. Cliquez sur l’onglet **Users** .

    c. Dans la zone de texte **User** , tapez l’adresse de messagerie de l’utilisateur.

    d. Cliquez sur **Assign** pour attribuer l’utilisateur à un rôle.

    e. Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Cloud Platform.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SAP Cloud Platform, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP Cloud Platform**.

    ![Lien SAP Cloud Platform dans la liste Applications](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SAP Cloud Platform dans le volet d’accès, vous devez être connecté automatiquement à votre application SAP Cloud Platform.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

