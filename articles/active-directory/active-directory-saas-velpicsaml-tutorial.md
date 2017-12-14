---
title: "Didacticiel : Intégration d’Azure Active Directory à Velpic SAML | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Velpic SAML."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: e4f7e4c9e960450f0024cd7ca35bd3808d31ee19
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Didacticiel : Intégration d’Azure Active Directory à Velpic SAML

Dans ce didacticiel, vous allez apprendre à intégrer Velpic SAML à Azure Active Directory (Azure AD).

L’intégration de Velpic SAML à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Velpic SAML
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Velpic SAML (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Velpic SAML, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Velpic SAML pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Velpic SAML à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-velpic-saml-from-the-gallery"></a>Ajout de Velpic SAML à partir de la galerie
Pour configurer l’intégration de Velpic SAML à Azure AD, vous devez ajouter Velpic SAML à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Velpic SAML à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Velpic SAML**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. Dans le volet des résultats, sélectionnez **Velpic SAML**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Velpic SAML avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Velpic SAML équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur associé dans Velpic SAML doit être établie.

Pour ce faire, affectez la valeur du champ **nom d’utilisateur** d’Azure AD comme valeur du champ **Username** (Nom d’utilisateur) dans Velpic SAML.

Pour configurer et tester l’authentification unique Azure AD avec Velpic SAML, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Velpic SAML](#creating-a-velpic-saml-test-user)** : pour avoir un équivalent de Britta Simon dans Velpic SAML lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Velpic SAML.

**Pour configurer l’authentification unique Azure AD avec Velpic SAML, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Velpic SAML**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Entrez les détails dans la section **Domaine et URL Velpic SAML** -

    ![Configurer l’authentification unique](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez la valeur suivante : `https://<sub-domain>.velpicsaml.net`

    b. Dans la zone de texte **Identificateur**, collez la valeur **URL d'authentification unique** `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Notez que l’URL d’authentification unique sera fournie par l’équipe Velpic SAML et la valeur Identificateur sera disponible lorsque vous configurez le plug-in SSO du côté Velpic SAML. Vous devez copier cette valeur à partir de la page d’application Velpic SAML et la coller ici.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. Dans la section Configuration de Velpic SAML, cliquez sur Configurer Velpic SAML pour ouvrir la fenêtre Configurer l’authentification. Copiez l’ID d’entité SAML à partir de la section Référence rapide.

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Velpic SAML en tant qu’administrateur.

8. Cliquez sur l’onglet **Gérer** et accédez à la section **Intégration** dans laquelle vous devez cliquer sur **Plug-ins** afin de créer le nouveau plug-in pour la connexion.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. Cliquez sur le bouton **Ajouter un plug-in**.
    
    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. Cliquez sur la mosaïque **SAML** dans la page Ajouter un plug-in.
    
    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. Entrez le nom du nouveau plug-in SAML puis cliquez sur le bouton **Ajouter**.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. Entrez les informations comme suit :

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. Dans la zone de texte **Nom**, saisissez le nom du plug-in SAML.

    b. Dans la zone de texte **URL de l’émetteur**, collez l**’ID d’entité SAML** que vous avez copié à partir de la fenêtre **Configurer l’authentification** du portail Azure.

    c. Dans la section **Configuration des métadonnées du fournisseur**, téléchargez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    d. Vous pouvez également choisir d’activer l’approvisionnement immédiat SAML en cochant la case **Créer automatiquement les nouveaux utilisateurs**. Si un utilisateur n’existe pas dans Velpic et que cet indicateur n’est pas activé, la connexion à partir d’Azure échouera. Si l’indicateur est activé, l’utilisateur sera automatiquement approvisionné dans Velpic au moment de la connexion. 

    e. Copiez l**’URL d’authentification unique** à partir de la zone de texte et collez-la dans le portail Azure.
    
    f. Cliquez sur **Save**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Création d’un utilisateur de test Velpic SAML

Cette étape n’est généralement pas nécessaire car l’application prend en charge l’approvisionnement immédiat de l’utilisateur. Si l’approvisionnement automatique de l’utilisateur n’est pas activé, la création manuelle de l’utilisateur peut être effectuée comme décrit ci-dessous.

Connectez-vous au site de votre entreprise Velpic SAML en tant qu’administrateur et effectuez les étapes suivantes :
    
1. Cliquez sur l’onglet Gérer et accédez à la section Utilisateurs, puis cliquez sur le bouton Nouveau pour ajouter des utilisateurs.

    ![ajouter un utilisateur](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. Dans la boîte de dialogue **Create New User**, procédez comme suit.

    ![user](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. Dans la zone de texte **First Name**, tapez le prénom de Britta Simon.

    b. Dans la zone de texte **Last Name**, tapez le nom de Britta Simon.

    c. Dans la zone de texte **User Name**, tapez le nom d’utilisateur de Britta Simon.

    d. Dans la zone de texte **E-mail** , tapez l’adresse de messagerie du compte de Britta Simon.

    e. Les autres informations sont facultatives et vous pouvez les renseigner si nécessaire.
    
    f. Cliquez sur **ENREGISTRER**.  

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Velpic SAML.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Velpic SAML, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Velpic SAML**.

    ![Configurer l’authentification unique](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

1. Lorsque vous cliquez sur la mosaïque Velpic SAML dans le volet d’accès, la page de connexion de l’application Velpic SAML devrait apparaître. Vous devriez voir le bouton **Se connecter à Azure AD** sur la page de connexion.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. Cliquez sur le bouton **Se connecter à Azure AD** pour vous connecter à Velpic à l’aide de votre compte Azure AD.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png

