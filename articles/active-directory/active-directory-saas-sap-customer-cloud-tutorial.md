---
title: "Didacticiel : Intégration d’Azure Active Directory à SAP Cloud pour le client | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud pour le client."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: b6ea3cfd8e50d82b5044708e78150fefb39b4c38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Didacticiel : Intégration d’Azure Active Directory avec SAP Cloud pour le client

Dans ce didacticiel, vous allez apprendre à intégrer SAP Cloud pour le client à Azure Active Directory (Azure AD).

L’intégration de SAP Cloud pour le client à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SAP Cloud pour le client.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SAP Cloud pour le client (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec SAP Cloud pour le client, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP Cloud for Customer pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP Cloud pour le client à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Ajout de SAP Cloud pour le client à partir de la galerie
Pour configurer l’intégration de SAP Cloud pour le client à Azure AD, vous devez ajouter SAP Cloud pour le client depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Cloud pour le client à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **SAP Cloud pour le client**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. Dans le panneau de résultats, sélectionnez **SAP Cloud for Customer**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Cloud pour le client, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Cloud pour le client équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SAP Cloud pour le client associé doit être établi.

Dans SAP Cloud pour le client, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec SAP Cloud pour le client, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SAP Cloud for Customer](#creating-a-sap-cloud-for-customer-test-user)** pour avoir un équivalent de Britta Simon dans SAP Cloud for Customer lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SAP Cloud for Customer.

**Pour configurer l’authentification unique Azure AD avec SAP Cloud pour le client, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **SAP Cloud for Customer**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. Dans la section **Domaine et URL SAP Cloud for Customer**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server name>.crm.ondemand.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). 

4. Dans la section **Attributs d’utilisateur**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. Dans la liste **Identificateur de l’utilisateur**, sélectionnez la fonction **ExtractMailPrefix()**.

    b. Dans la liste **Courrier** , sélectionnez l’attribut utilisateur que vous souhaitez utiliser pour votre implémentation.
    Par exemple, si vous souhaitez utiliser EmployeeID comme identificateur d’utilisateur unique et que vous avez stocké la valeur d’attribut dans ExtensionAttribute2, sélectionnez user.extensionattribute2.  

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de SAP Cloud for Customer**, cliquez sur **Configurer SAP Cloud pour le client** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Pour configurer l’authentification unique, procédez comme suit :
   
    a. Connectez-vous au portail SAP Cloud pour le client avec des droits d’administrateur.
   
    b. Accédez à **Application and User Management Common Task** (Tâche courante d’application et de gestion des utilisateurs) et cliquez sur l’onglet **Identity Provider** (Fournisseur d’identité).
   
    c. Cliquez sur **New Identity Provider** (Nouveau fournisseur d’identité) et sélectionnez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure. En important les métadonnées, le système charge automatiquement le certificat de signature ainsi que le certificat de chiffrement requis.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory nécessitant l’URL Assertion Consumer Service dans la requête SAML, cochez la case **Include Assertion Consumer Service URL** (Inclure l’URL Assertion Consumer Service).
   
    e. Cliquez sur **Activate Single Sign-On**(Activer l’authentification unique).
   
    f. Enregistrez vos modifications.
   
    g. Cliquez sur l’onglet **My System** (Mon système).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. Dans la zone de texte **Azure AD Sign On URL** (URL de connexion Azure AD), collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Précisez si l’employé peut choisir manuellement entre l’authentification à l’aide d’un ID d’utilisateur/mot de passe ou l’authentification unique en cliquant sur **Manual Identity Provider Selection**(Sélection manuelle du fournisseur d’identité).
   
    j. Dans la section **URL SSO** , spécifiez l’URL devant être utilisée par vos employés pour se connecter au système. 
    Dans la liste **URL Sent to Employee** (URL envoyée à l’employé), vous pouvez choisir entre les options suivantes :
   
    **Non-SSO URL**
   
    Le système envoie uniquement l’URL du système normale à l’employé. L’employé ne peut pas se connecter à l’aide de l’authentification unique ; il doit donc utiliser un mot de passe ou un certificat.
   
    **URL SSO** 
   
    Le système envoie uniquement l’URL SSO à l’employé. L’employé peut se connecter à l’aide de l’authentification unique. La demande d’authentification est redirigée via l’IdP.
   
    **Sélection automatique**
   
    Si l’authentification unique n’est pas activée, le système envoie l’URL du système normale à l’employé. Si l’authentification unique est activée, le système vérifie si l’employé possède un mot de passe. Le cas échéant, les URL SSO et les URL non SSO sont envoyées à l’employé. Toutefois, si l’employé ne possède aucun mot de passe, seule l’URL SSO est envoyée à l’employé.
   
    k. Enregistrez vos modifications.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Création d’un utilisateur de test SAP Cloud for Customer

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SAP Cloud pour le client. Collaborez avec l’[équipe de support technique SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) pour ajouter les utilisateurs dans la plateforme SAP Cloud for Customer. 

> [!NOTE]
> Assurez-vous que la valeur NameID correspond au champ de nom d’utilisateur dans la plateforme SAP Cloud pour le client.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Cloud for Customer.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SAP Cloud pour le client, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Cloud SAP pour le client**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SAP Cloud pour le client dans le volet d’accès, vous devez être connecté automatiquement à votre application SAP Cloud pour le client.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png

