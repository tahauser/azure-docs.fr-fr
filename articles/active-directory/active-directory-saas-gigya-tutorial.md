---
title: "Didacticiel : Intégration d’Azure Active Directory à Gigya | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Gigya."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 74bc67fb90f0505d2e1f213a4a0cdc26ade22872
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Didacticiel : Intégration d’Azure Active Directory à Gigya

Dans ce didacticiel, vous allez apprendre à intégrer Gigya à Azure Active Directory (Azure AD).

L’intégration de Gigya à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Gigya.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Gigya (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Gigya, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Gigya pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Gigya à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-gigya-from-the-gallery"></a>Ajout de Gigya à partir de la galerie
Pour configurer l’intégration de Gigya à Azure AD, vous devez ajouter Gigya à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Gigya à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Gigya**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_search.png)

5. Dans le panneau de résultats, sélectionnez **Gigya**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Gigya à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Gigya correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Gigya associé doit être établie.

Dans Gigya, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Gigya, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Gigya](#creating-a-gigya-test-user)** pour avoir un équivalent de Britta Simon dans Gigya lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Gigya.

**Pour configurer l’authentification unique Azure AD avec Gigya, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Gigya**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_samlbase.png)

3. Dans la section **Gigya Domain and URLs** (Domaine et URL Gigya), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `http://<companyname>.gigya.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Gigya](https://www.gigya.com/support-policy/). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/tutorial_general_400.png)

6. Dans la section **Gigya Configuration** (Configuration de Gigya), cliquez sur **Configure Gigya** (Configurer Gigya) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Gigya en tant qu’administrateur.

8. Accédez à **Settings \> SAML Login**, puis cliquez sur le bouton **Add**.
   
    ![Connexion SAML](./media/active-directory-saas-gigya-tutorial/ic789532.png "Connexion SAML")

9. Dans la section **SAML Login** , procédez comme suit :
   
    ![Configuration SAML](./media/active-directory-saas-gigya-tutorial/ic789533.png "Configuration SAML")
   
    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.
   
    b. Dans la zone de texte **Émetteur**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis le portail Azure. 
   
    c. Dans la zone de texte **URL du service d’authentification unique**, collez la valeur de **l’URL du service d’authentification unique** que vous avez copiée à partir du portail Azure.
   
    d. Dans la zone de texte **Name ID Format** (Format d’ID de nom), collez la valeur de **Format de l’identificateur du nom** que vous avez copiée à partir du portail Azure.
   
    e. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.
   
    f. Cliquez sur **Save Settings**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-gigya-test-user"></a>Création d’un utilisateur de test Gigya

Pour se connecter à Gigya, les utilisateurs d’Azure AD doivent être approvisionnés dans Gigya.  
Dans le cas de Gigya, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous au site d’entreprise **Gigya** en tant qu’administrateur.

2. Accédez à **Admin \> Manage Users**, puis cliquez sur **Invite Users**.
   
    ![Gestion des utilisateurs](./media/active-directory-saas-gigya-tutorial/ic789535.png "Gestion des utilisateurs")

3. Dans la boîte de dialogue Invite Users, procédez comme suit :
   
    ![Inviter des utilisateurs](./media/active-directory-saas-gigya-tutorial/ic789536.png "Inviter des utilisateurs")
   
    a. Dans la zone de texte **Email** , tapez l’alias de courrier électronique d’un compte Azure Active Directory valide à approvisionner.
    
    b. Cliquez sur **Invite User**.
      
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Gigya.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Gigya, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Gigya**.

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Gigya dans le panneau d’accès, vous devez être connecté automatiquement à votre application Gigya.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_203.png

