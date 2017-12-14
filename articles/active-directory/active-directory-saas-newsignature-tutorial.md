---
title: "Didacticiel : Intégration d’Azure Active Directory à Cloud Management Portal for Microsoft Azure | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cloud Management Portal for Microsoft Azure."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 5eabff9399f35328feb973ad4a0abbf793fe7458
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Didacticiel : Intégration d’Azure Active Directory à Cloud Management Portal for Microsoft Azure

Dans ce didacticiel, vous allez apprendre à intégrer Cloud Management Portal for Microsoft Azure à Azure Active Directory (Azure AD).

L’intégration du portail de gestion cloud de Microsoft Azure avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Cloud Management Portal for Microsoft Azure.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Cloud Management Portal for Microsoft Azure (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Cloud Management Portal for Microsoft Azure, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Cloud Management Portal for Microsoft Azure pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Cloud Management Portal for Microsoft Azure à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Ajout de Cloud Management Portal for Microsoft Azure à partir de la galerie
Pour configurer l’intégration de Cloud Management Portal for Microsoft Azure à Azure AD, vous devez ajouter Cloud Management Portal for Microsoft Azure à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Cloud Management Portal for Microsoft Azure à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Cloud Management Portal for Microsoft Azure**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_search.png)

5. Dans le volet de résultats, sélectionnez **Cloud Management Portal for Microsoft Azure**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cloud Management Portal for Microsoft Azure avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Cloud Management Portal for Microsoft Azure équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Cloud Management Portal for Microsoft Azure associé doit être établi.

Dans Cloud Management Portal for Microsoft Azure, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Cloud Management Portal for Microsoft Azure, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Cloud Management Portal for Microsoft Azure](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)** pour avoir un équivalent de Britta Simon dans Cloud Management Portal for Microsoft Azure lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Cloud Management Portal for Microsoft Azure.

**Pour configurer l’authentification unique Azure AD avec Cloud Management Portal for Microsoft Azure, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Cloud Management Portal for Microsoft Azure**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. Dans la section **Domaine et URL Cloud Management Portal for Microsoft Azure**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. Dans la zone de texte **URL de réponse** , tapez une URL en respectant les formats suivants : 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support client Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-newsignature-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Cloud Management Portal for Microsoft Azure**, cliquez sur **Configurer Cloud Management Portal for Microsoft Azure** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Pour configurer l’authentification unique côté **Cloud Management Portal for Microsoft Azure**, vous devez envoyer le **certificat** téléchargé, **l’URL de déconnexion**, **l’URL du service d’authentification unique SAML** et **l’ID d’entité SAML** à [l’équipe du support technique Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com). Elle configure ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Création d’un utilisateur de test Cloud Management Portal for Microsoft Azure

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Cloud Management Portal for Microsoft Azure. Collaborez avec [l’équipe du support technique Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) pour ajouter les utilisateurs au compte Cloud Management Portal for Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cloud Management Portal for Microsoft Azure.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Cloud Management Portal for Microsoft Azure, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Cloud Management Portal for Microsoft Azure**.

    ![Configurer l’authentification unique](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
Quand vous cliquez sur la vignette Cloud Management Portal for Microsoft Azure dans le volet d’accès, vous devez être connecté automatiquement à votre application Cloud Management Portal for Microsoft Azure.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png

