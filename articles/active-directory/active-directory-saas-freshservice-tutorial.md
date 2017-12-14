---
title: "Didacticiel : intégration d’Azure Active Directory à Freshservice | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Freshservice."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 9362c68ea2c05c231921daaa0557c454b5d28d0b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Didacticiel : intégration d’Azure Active Directory à Freshservice

Dans ce didacticiel, vous allez apprendre à intégrer Freshservice à Azure Active Directory (Azure AD).

L’intégration de Freshservice à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Freshservice.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Freshservice (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Freshservice, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Freshservice pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Freshservice à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-freshservice-from-the-gallery"></a>Ajout de Freshservice à partir de la galerie
Pour configurer l’intégration de Freshservice à Azure AD, vous devez ajouter Freshservice disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Freshservice à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Freshservice**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_search.png)

5. Dans le volet de résultats, sélectionnez **Freshservice**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Freshservice, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Freshservice équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Freshservice associé doit être établie.

Dans Freshservice, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Freshservice, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Freshservice](#creating-a-freshservice-test-user)** pour avoir un équivalent de Britta Simon dans Freshservice lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Freshservice.

**Pour configurer l’authentification unique Azure AD avec Freshservice, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Freshservice**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. Dans la section **Domaine et URL Freshservice**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<democompany>.freshservice.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support client Freshservice](https://support.freshservice.com/). 
 
4. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT** du certificat.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Freshservice**, cliquez sur **Configurer Freshservice** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshservice en tant qu’administrateur.

8. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Administrateur](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Administrateur")

9. Dans le **Customer Portal**, cliquez sur **Security**.
   
    ![Sécurité](./media/active-directory-saas-freshservice-tutorial/ic790815.png "Sécurité")

10. Dans la section **Security** , procédez comme suit :
   
    ![Single Sign On](./media/active-directory-saas-freshservice-tutorial/ic790816.png "Single Sign On")
   
    a. Activez **Single Sign On**.

    b. Sélectionnez **SAML SSO**.

    c. Dans la zone de texte **URL de connexion SAML**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **URL de déconnexion**, collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Security Certificate Fingerprint** (Empreinte du certificat de sécurité), collez la valeur **THUMBPRINT** du certificat que vous avez copiée à partir du portail Azure.

    f. Cliquez sur **Enregistrer**.
   
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-freshservice-test-user"></a>Création d’un utilisateur de test Freshservice

Pour permettre aux utilisateurs Azure AD de se connecter à Freshservice, vous devez les approvisionner dans Freshservice. Dans le cas de FreshService, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise **FreshService** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Administrateur](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Administrateur")

3. Dans la section **User Management**, cliquez sur **Requesters**.
   
    ![Requesters](./media/active-directory-saas-freshservice-tutorial/ic790818.png "Requesters")

4. Cliquez sur **New Requester**.
   
    ![New Requesters](./media/active-directory-saas-freshservice-tutorial/ic790819.png "New Requesters")

5. Dans la section **New Requester** , procédez comme suit :
   
    ![New Requester](./media/active-directory-saas-freshservice-tutorial/ic790820.png "New Requester")   

    a. Entrez le prénom et l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte **Prénom** et **Email**.

    b. Cliquez sur **Save**.
   
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé.
    >  

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par FreshService, pour approvisionner des comptes utilisateur AAD.
>  

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Freshservice, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Freshservice**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Freshservice dans le volet d’accès, vous devez être connecté automatiquement à votre application Freshservice.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_203.png

