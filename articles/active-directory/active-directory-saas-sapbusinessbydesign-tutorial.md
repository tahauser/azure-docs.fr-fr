---
title: "Didacticiel : intégration d’Azure Active Directory à SAP Business ByDesign | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Business ByDesign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: bc090dfc2dd0833b1ff85ccb87b5a1a2c9fa4ac4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Didacticiel : Intégration d’Azure Active Directory à SAP Business ByDesign

Dans ce didacticiel, vous allez apprendre à intégrer SAP Business ByDesign à Azure Active Directory (Azure AD).

L’intégration de SAP Business ByDesign à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez déterminer qui a accès à SAP Business ByDesign.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SAP Business ByDesign (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec SAP Business ByDesign, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP Business ByDesign pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter SAP Business ByDesign à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Ajouter SAP Business ByDesign à partir de la galerie
Pour configurer l’intégration de SAP Business ByDesign à Azure AD, vous devez ajouter SAP Business ByDesign depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Business ByDesign à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez **SAP Business ByDesign.**, sélectionnez **SAP Business ByDesign.** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SAP Business ByDesign dans la liste des résultats](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Business ByDesign, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Business ByDesign équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SAP Business ByDesign associé doit être établi.

Dans SAP Business ByDesign, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SAP Business ByDesign, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)** pour avoir un équivalent de Britta Simon dans SAP Business ByDesign, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SAP Business ByDesign.

**Pour configurer l’authentification unique Azure AD avec SAP Business ByDesign, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SAP Business ByDesign**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. Dans la section **Domaine et URL SAP Business ByDesign**, procédez comme suit :

    ![Informations d’authentification unique dans la section Domaine et URL SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<servername>.sapbydesign.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html).

4. Dans la section **Attributs d’utilisateur**, procédez comme suit :

    ![Section d’attribut de SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. Dans la liste **Identificateur de l’utilisateur**, sélectionnez la fonction **ExtractMailPrefix()**.
    
    b. Dans la liste **Courrier** , sélectionnez l’attribut utilisateur que vous souhaitez utiliser pour votre implémentation. Par exemple, si vous souhaitez utiliser EmployeeID comme identificateur d’utilisateur unique et que vous avez stocké la valeur d’attribut dans ExtensionAttribute2, sélectionnez user.extensionattribute2.     

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de SAP Business ByDesign**, cliquez sur **Configurer SAP Business ByDesign** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Pour configurer l’authentification unique pour votre application, procédez comme suit :
   
    a. Connectez-vous à votre portail SAP Business ByDesign avec des droits d’administrateur.
   
    b. Accédez à **Application and User Management Common Task (Tâche courante d’application et de gestion des utilisateurs)** et cliquez sur l’onglet **Identity Provider (Fournisseur d’identité)**.
   
    c. Cliquez sur **New Identity Provider** (Nouveau fournisseur d’identité) et sélectionnez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure. En important les métadonnées, le système charge automatiquement le certificat de signature ainsi que le certificat de chiffrement requis.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Pour inclure **l’URL Assertion Consumer Service** dans la requête SAML, sélectionnez **Include Assertion Consumer Service URL (Inclure l’URL Assertion Consumer Service)**.
   
    e. Cliquez sur **Activate Single Sign-On**(Activer l’authentification unique).
   
    f. Enregistrez vos modifications.
   
    g. Cliquez sur l’onglet **My System** (Mon système).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Dans la zone de texte de **l’URL du service d’authentification unique Azure AD**, collez **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Précisez si l’employé peut choisir manuellement entre l’authentification à l’aide d’un ID d’utilisateur/mot de passe ou l’authentification unique en cliquant sur **Manual Identity Provider Selection**(Sélection manuelle du fournisseur d’identité).
   
    j. Dans la section **URL SSO** , spécifiez l’URL devant être utilisée par l’employé pour se connecter au système. 
    Dans la liste déroulante URL Sent to Employee (URL envoyée à l’employé), vous pouvez choisir entre les options suivantes :
   
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

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Créer un utilisateur de test pour SAP Business ByDesign

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SAP Business ByDesign. Veuillez contacter [l’équipe de prise en charge des clients de SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) pour ajouter des utilisateurs sur la plateforme SAP Business ByDesign. 

> [!NOTE]
> Assurez-vous que la valeur NameID correspond au champ de nom d’utilisateur dans la plateforme SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Business ByDesign.

![Assigner le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à SAP Business ByDesign, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP Business ByDesign**.

    ![Lien correspondant à SAP Business ByDesign dans la liste des applications](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SAP Business ByDesign dans le volet d’accès, vous devez être connecté automatiquement à votre application SAP Business ByDesign.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png

