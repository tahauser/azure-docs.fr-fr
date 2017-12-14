---
title: "Didacticiel : Intégration d’Azure Active Directory à Insignia SAML SSO | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Insignia SAML SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 828c981c-c3dd-4eb2-8699-0f732baa43f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: jeedes
ms.openlocfilehash: 9d7d780e212a409c1b868b675e190c4a03788ab1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-insignia-saml-sso"></a>Didacticiel : Intégration d’Azure Active Directory à Insignia SAML SSO

Dans ce didacticiel, vous allez apprendre à intégrer Insignia SAML SSO à Azure Active Directory (Azure AD).

L’intégration d’Insignia SAML SSO dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Insignia SAML SSO.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Insignia SAML SSO (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Insignia SAML SSO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Insignia SAML SSO pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Insignia SAML SSO à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-insignia-saml-sso-from-the-gallery"></a>Ajout d’Insignia SAML SSO à partir de la galerie
Pour configurer l’intégration d’Insignia SAML SSO à Azure AD, vous devez ajouter Insignia SAML SSO à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Insignia SAML SSO à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Insignia SAML SSO**, sélectionnez **Insignia SAML SSO** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Insignia SAML SSO dans la liste des résultats](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_insigniasamlsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’Insignia SAML SSO avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Insignia SAML SSO équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Insignia SAML SSO associé doit être établie.

Dans Insignia SAML SSO, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD auprès d’Insignia SAML SSO, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Insignia SAML SSO](#create-an-insignia-saml-sso-test-user)** pour avoir un équivalent de Britta Simon dans Insignia SAML SSO lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Insignia SAML SSO.

**Pour configurer l’authentification unique Azure AD auprès d’Insignia SAML SSO, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Insignia SAML SSO**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_insigniasamlsso_samlbase.png)

3. Dans la section **Domaine et URL Insignia SAML SSO**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Insignia SAML SSO](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_insigniasamlsso_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<customername>.insigniails.com/ils` |
    | `https://<customername>.insigniails.com/` |
    | `https://<customername>.insigniailsusa.com/ ` |

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<customername>.insigniailsusa.com/<uniqueid>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support Insignia SAML SSO](http://www.insigniasoftware.com/insignia/Techsupport.aspx). 
 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_insigniasamlsso_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Insignia SAML SSO**, cliquez sur **Configurer Insignia SAML SSO** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration d’Insignia SAML SSO](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_insigniasamlsso_configure.png) 

7. Pour configurer l’authentification unique côté **Insignia SAML SSO**, vous devez envoyer le **certificat (Base64)** téléchargé, l’**URL de déconnexion et l’URL du service d’authentification unique SAML** à l’[équipe de support Insignia SAML SSO](http://www.insigniasoftware.com/insignia/Techsupport.aspx). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-insigniasamlsso-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-insigniasamlsso-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-insigniasamlsso-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-insigniasamlsso-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-insignia-saml-sso-test-user"></a>Créer un utilisateur de test Insignia SAML SSO

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Insignia Library System. Travaillez avec l’[équipe de support Insignia Library System](http://www.insigniasoftware.com/insignia/Techsupport.aspx) pour ajouter des utilisateurs dans la plateforme Insignia Library System.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Insignia SAML SSO.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Insignia SAML SSO, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Insignia SAML SSO**.

    ![Lien Insignia SAML SSO dans la liste des applications](./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_insigniasamlsso_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Insignia SAML SSO dans le volet d’accès, vous devez être connecté automatiquement à votre application Insignia SAML SSO.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-insigniasamlsso-tutorial/tutorial_general_203.png

