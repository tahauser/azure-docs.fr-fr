---
title: "Didacticiel : Intégration d’Azure Active Directory à DATABASICS | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et DATABASICS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a37ded45-84c8-4e88-8d9b-c5b9443eb0d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: jeedes
ms.openlocfilehash: 3758af9a9ef29dc344af25acd8db75539de46e12
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-databasics"></a>Didacticiel : Intégration d’Azure Active Directory à DATABASICS

Dans ce didacticiel, vous allez apprendre à intégrer DATABASICS à Azure Active Directory (Azure AD).

L’intégration de DATABASICS à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à DATABASICS.
- Vous pouvez permettre à vos utilisateurs d’être automatiquement authentifié sur DATABASICS (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD DATABASICS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement DATABASICS pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter DATABASICS à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-databasics-from-the-gallery"></a>Ajouter DATABASICS à partir de la galerie
Pour configurer l’intégration de DATABASICS à Azure AD, vous devez ajouter DATABASICS à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter DATABASICS à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **DATABASICS**, sélectionnez **DATABASICS** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![DATABASICS dans la liste des résultats](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec DATABASICS pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur DATABASICS équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur DATABASICS associé doit être établie.

Dans DATABASICS, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec DATABASICS, vous devez suivre les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test DATABASICS](#create-a-databasics-test-user)** pour avoir un équivalent de Britta Simon dans DATABASICS lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application DATABASICS.

**Pour configurer l’authentification unique Azure AD avec DATABASICS, suivez les étapes ci-dessous :**

1. Sur la page d’intégration de l’application **DATABASICS** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_samlbase.png)

3. Dans la section **Domaine et URL DATABASICS**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL DATABASICS](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_url.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur : `DATA-BASICS_SP`
    
    b. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<sitenumber>.data-basics.net/<clientname>/saml_sso.jsp`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez [l’équipe de support client DATABASICS](https://www.data-basics.com/support/) pour obtenir ces valeurs.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-databasics-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté DATABASICS, complétez le formulaire à l’aide de l’URL ci-dessous. Une fois que le formulaire est envoyé, l’[équipe de support DATABASICS](https://www.data-basics.com/support/) vous contacte.
    
    [https://www.data-basics.com/support/submit-sso-onboarding-request/](https://www.data-basics.com/support/submit-sso-onboarding-request/)


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-databasics-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-databasics-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-databasics-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-databasics-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-databasics-test-user"></a>Créer un utilisateur de test DATABASICS

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans DATABASICS. Travaillez avec [l’équipe de support DATABASICS](https://www.data-basics.com/support/) pour ajouter les utilisateurs à la plateforme DATABASICS. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à DATABASICS.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à DATABASICS, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **DATABASICS**.

    ![Lien DATABASICS dans la liste des applications](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Vous devriez être automatiquement authentifié sur votre application DATABASICS lorsque vous cliquez sur la vignette DATABASICS dans le volet d’accès.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_203.png

