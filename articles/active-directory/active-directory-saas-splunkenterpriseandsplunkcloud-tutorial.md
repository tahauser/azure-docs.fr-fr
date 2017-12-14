---
title: "Didacticiel : intégration d’Azure Active Directory à Splunk Enterprise et Splunk Cloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Splunk Enterprise et Splunk Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: fe9a995a32f632c47aeed6d3f12d37a224823f06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Didacticiel : Intégration d’Azure Active Directory avec Splunk Enterprise et Splunk Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Splunk Enterprise et Splunk Cloud à Azure Active Directory (Azure AD).

L’intégration de Splunk Enterprise et Splunk Cloud à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Splunk Enterprise et Splunk Cloud
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Splunk Enterprise et Splunk Cloud (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Splunk Enterprise et Splunk Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Splunk Enterprise et Splunk Cloud avec l’authentification unique activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Splunk Enterprise et Splunk Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Ajout de Splunk Enterprise et Splunk Cloud à partir de la galerie
Pour configurer son intégration à Azure AD, vous devez ajouter Splunk Enterprise et Splunk Cloud à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Splunk Enterprise et Splunk Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Splunk Enterprise et Splunk Cloud**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_search.png)

5. Dans le volet des résultats, sélectionnez **Splunk Enterprise et Splunk Cloud**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Splunk Enterprise et Splunk Cloud, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Splunk Enterprise et Splunk Cloud équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et un utilisateur Splunk Enterprise et Splunk Cloud associé.

Dans Splunk Enterprise and Splunk Cloud, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Splunk Enterprise et Splunk Cloud, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Splunk Enterprise et Splunk Cloud](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Splunk Enterprise et Splunk Cloud, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Splunk Enterprise et Splunk Cloud.

**Pour configurer l’authentification unique Azure AD avec Splunk Enterprise et Splunk Cloud, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Splunk Enterprise and Splunk Cloud**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_samlbase.png)

3. Dans la section **Domaines et URL Splunk Enterprise and Splunk Cloud**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité (IDP)** :

    ![Configurer l’authentification unique](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<splunkserverUrl>/en-US/app/launcher/home`
    
    b. Dans la zone de texte **Identificateur**, entrez de votre serveur Splunk.

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<splunkserver>/saml/acs`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Pour obtenir ces valeurs, contactez l’[équipe de support technique Splunk Enterprise and Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **Splunk Enterprise et Splunk Cloud**, vous devez envoyer les **métadonnées XML** téléchargées à l’[équipe de support Splunk Enterprise and Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Création d’un utilisateur de test Splunk Enterprise and Splunk Cloud

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Splunk Enterprise et Splunk Cloud. Veuillez travailler avec l’[équipe de support technique de Splunk Enterprise and Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support) pour ajouter des utilisateurs dans la plateforme Splunk Enterprise and Splunk Cloud.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Splunk Enterprise and Splunk Cloud.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Splunk Enterprise et Splunk Cloud, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Splunk Enterprise et Splunk Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Splunk Enterprise et Splunk Cloud dans le volet d’accès, vous devez être connecté automatiquement à votre application Splunk Enterprise et Splunk Cloud.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_203.png

