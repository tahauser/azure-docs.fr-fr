---
title: "Didacticiel : Intégration d’Azure Active Directory à AnswerHub | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et AnswerHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: b5e367c35582d569dc592aaf09034e39bfb0fa65
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Didacticiel : Intégration d’Azure Active Directory à AnswerHub

Dans ce didacticiel, vous allez apprendre à intégrer AnswerHub à Azure Active Directory (Azure AD).

L’intégration d’AnswerHub dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à AnswerHub
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à AnswerHub (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à AnswerHub, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement AnswerHub pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’AnswerHub à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-answerhub-from-the-gallery"></a>Ajout d’AnswerHub à partir de la galerie
Pour configurer l’intégration d’AnswerHub à Azure AD, vous devez ajouter AnswerHub depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter AnswerHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **AnswerHub**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_search.png)

5. Dans le volet de résultats, sélectionnez **AnswerHub**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et testez l’authentification unique Azure AD avec AnswerHub au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur AnswerHub équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur AnswerHub associé doit être établie.

Dans AnswerHub, affectez la valeur du **nom d’utilisateur** indiquée dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec AnswerHub, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test AnswerHub](#creating-an-answerhub-test-user)** pour avoir dans AnswerHub un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application AnswerHub.

**Pour configurer l’authentification unique Azure AD avec AnswerHub, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **AnswerHub**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. Dans la section **Domaine et URL AnswerHub**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company>.answerhub.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company>.answerhub.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’AnswerHub](mailto:success@answerhub.com). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration AnswerHub**, cliquez sur **Configurer AnswerHub** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AnswerHub en tant qu’administrateur.
   
    >[!NOTE]
    >Si vous avez besoin d’aide pour la configuration d’AnswerHub, contactez [l’équipe de support technique de AnswerHub](mailto:success@answerhub.com.).
   
8. Accédez à **Administration**.

9. Cliquez sur l’onglet **Users &amp; Groups** .

10. Dans le volet de navigation situé sur le côté gauche, dans la section **Social Settings**, cliquez sur **SAML Setup**.

11. Cliquez sur l’onglet **IDP Config** .

12. Sous l’onglet **IDP Config** , procédez comme suit :

     ![Configuration de SAML](./media/active-directory-saas-answerhub-tutorial/ic785172.png "Configuration de SAML")  
  
     a. Dans la zone de texte **IDP Login URL** (URL de connexion du fournisseur d’identité), collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
  
     b. Dans la zone de texte **IDP Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
     
     c. Dans la zone de texte **IDP Name Identifier Format** (Format de l’identificateur du nom IDP), entrez la valeur de l’identificateur d’utilisateur telle que sélectionnée dans le portail Azure à la section **Attributs utilisateur**.
  
     d. Cliquez sur **Keys and Certificates**.

13. Sous l’onglet Keys and Certificates, procédez comme suit :
    
     ![Clés et certificats](./media/active-directory-saas-answerhub-tutorial/ic785173.png "Clés et certificats")  
 
     a. Dans le Bloc-notes, ouvrez le certificat codé en base 64 que vous avez téléchargé depuis le portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IDP Public Key (x509 Format)** (Clé publique IDP au format x509).
  
     b. Cliquez sur **Save**.

14. Sous l’onglet **IDP Config**, cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-answerhub-test-user"></a>Création d’un utilisateur de test AnswerHub

Pour permettre aux utilisateurs Azure AD de se connecter à AnswerHub, vous devez les approvisionner dans AnswerHub.  
Dans le cas d’AnswerHub, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **AnswerHub** en tant qu’administrateur.

2. Accédez à **Administration**.

3. Cliquez sur l’onglet **Users & Groups**.

4. Dans le volet de navigation situé sur le côté gauche, dans la section **Manage Users**, cliquez sur **Create or import users**.
   
   ![Utilisateurs et groupes](./media/active-directory-saas-answerhub-tutorial/ic785175.png "Utilisateurs et groupes")

5. Tapez l’adresse e-mail, le nom d’utilisateur et le mot de passe du compte d’utilisateur Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email address**, **Username** et **Password**, puis cliquez sur **Save**.

>[!NOTE]
>Vous pouvez utiliser n'importe quel autre outil ou API de création de compte d’utilisateur AnswerHub fourni par ce service pour approvisionner des comptes utilisateurs AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à AnswerHub.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à AnswerHub, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **AnswerHub**.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette AnswerHub dans le volet d’accès, vous devez automatiquement être connecté à votre application AnswerHub.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_203.png

