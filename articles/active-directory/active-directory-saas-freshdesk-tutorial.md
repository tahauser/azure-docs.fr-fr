---
title: "Didacticiel : Intégration d’Azure Active Directory avec FreshDesk | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et FreshDesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 232058fc35c8206c4d8f4e990d1aea5d3d9a5a69
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Intégration d’Azure Active Directory à FreshDesk

Dans ce didacticiel, vous allez apprendre à intégrer FreshDesk à Azure Active Directory (Azure AD).

L’intégration de FreshDesk dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à FreshDesk
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FreshDesk (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à FreshDesk, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FreshDesk pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FreshDesk depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-freshdesk-from-the-gallery"></a>Ajout de FreshDesk depuis la galerie
Pour configurer l’intégration de FreshDesk dans Azure AD, vous devez ajouter FreshDesk depuis la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter FreshDesk à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **FreshDesk**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. Dans le panneau de résultats, sélectionnez **FreshDesk**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et testez l’authentification unique Azure AD avec FreshDesk avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur FreshDesk correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur FreshDesk associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** dans FreshDesk.

Pour configurer et tester l’authentification unique Azure AD avec FreshDesk, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test FreshDesk](#creating-a-freshdesk-test-user)** pour avoir un équivalent de Britta Simon dans FreshDesk, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail de gestion Azure et configurer l’authentification unique dans votre application FreshDesk.

**Procédez comme suit pour configurer l’authentification unique Azure AD avec FreshDesk :**

1. Dans le Portail de gestion Azure, sur la page d’intégration de l’application **FreshDesk**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Dans la section **Domaine et URL FreshDesk**, entrez **l’URL de connexion** en tant que `https://<tenant-name>.freshdesk.com` ou toute autre valeur suggérée par Freshdesk.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe de support FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) pour obtenir cette valeur.  

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat** puis enregistrez le certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de FreshDesk** , cliquez sur **Configurer FreshDesk** pour ouvrir la fenêtre Configurer l’authentification. Copiez l’URL du service d’authentification unique SAML et l’URL de déconnexion à partir de la section **Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshdesk en tant qu’administrateur.

8. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administrateur")

9. Dans l’onglet **General Settings**, cliquez sur **Security**.
   
   ![Sécurité](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sécurité")

10. Dans la section **Security** , procédez comme suit :
   
    ![Single Sign On](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")
   
    a. Pour **Single Sign On (SSO)**, sélectionnez **On**.

    b. Sélectionnez **SAML SSO**.

    c. Saisissez **l’URL de service d’authentification unique SAML** que vous avez copiée à partir du portail Azure dans la zone de texte **URL de connexion SAML**.

    d. Saisissez **l’URL de déconnexion** que vous avez copiée à partir du portail Azure dans la zone de texte **URL de déconnexion**.

    e. Copiez la valeur de **Empreinte** dans le certificat téléchargé depuis le portail Azure, puis collez-la dans la zone de texte **Security Certificate Fingerprint**.  
 
    >[!TIP]
    >Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI). 
    
    f. Cliquez sur **Save**.


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-freshdesk-test-user"></a>Création d’un utilisateur de test FreshDesk

Pour permettre aux utilisateurs Azure AD de se connecter à FreshDesk, vous devez les approvisionner dans FreshDesk.  
Dans le cas de FreshDesk, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Freshdesk** .
2. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administrateur")

3. Dans l’onglet **General Settings**, cliquez sur **Agents**.
   
   ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4. Cliquez sur **New Agent**.
   
    ![New Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")

5. Dans la boîte de dialogue Agent Information, procédez comme suit :
   
   ![Agent Information](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   a. Dans la zone de texte **Full Name** , tapez le nom du compte Azure AD que vous souhaitez approvisionner.

   b. Dans la zone de texte **Email** , tapez l’adresse de messagerie Azure AD du compte Azure AD que vous souhaitez approvisionner.

   c. Dans la zone de texte **Title** , tapez le titre du compte Azure AD que vous souhaitez approvisionner.

   d. Sélectionnez **Agents role**, puis cliquez sur **Assign**.
       
   e. Cliquez sur **Save**.     
   
    >[!NOTE]
    >Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé. 
    > 
    
    >[!NOTE]
    >Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Freshdesk, pour approvisionner des comptes utilisateur AAD. à FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Box.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à FreshDesk, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **FreshDesk**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette FreshDesk dans le volet d’accès vous connecte automatiquement à votre application FreshDesk.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

