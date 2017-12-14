---
title: "Didacticiel : Intégration d’Azure Active Directory dans Asana | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Asana."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: 8058dcd397e5f81f4a8c8cd1845353fd789f604b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Didacticiel : Intégration d’Azure Active Directory dans Asana

Dans ce didacticiel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) dans Asana.

L’intégration d’Azure AD dans Asana offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Asana.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Asana (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Asana, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Asana avec authentification unique activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Asana à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-asana-from-the-gallery"></a>Ajout d’Asana à partir de la galerie
Pour configurer l’intégration d’Asana dans Azure AD, vous devez ajouter Asana à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter Asana à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Asana**, sélectionnez **Asana** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Asana, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Asana équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Asana associé doit être établie.

Dans Asana, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Asana, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Asana](#create-an-asana-test-user)** pour avoir un équivalent de Britta Simon dans Asana, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Asana.

**Pour configurer l’authentification unique Azure AD avec Asana, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Asana**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. Dans la section **Domaine et URL Asana**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.asana.com/`

    b. Dans la zone de texte **Identificateur**, tapez la valeur : `https://app.asana.com/`
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Asana**, cliquez sur **Configurer Asana** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration d’Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. Dans une autre fenêtre de navigateur, connectez-vous à votre application Asana. Pour configurer l’authentification unique dans Asana, accédez aux paramètres de l’espace de travail en cliquant sur son nom dans le coin supérieur droit de l’écran. Ensuite, cliquez sur le **\<nom de votre espace de travail\> Paramètres**. 
   
    ![Paramètre d’authentification unique Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. Dans la fenêtre **Paramètres de l’organisation**, cliquez sur **Administration**. Ensuite, cliquez sur **Members must log in via SAML** (Les membres doivent se connecter via SAML) pour configurer l’authentification unique. Puis, procédez comme suit :
   
    ![Configurer les paramètres d’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. Dans la zone de texte **URL de la page de connexion**, collez **l’URL du service d’authentification unique SAML**.

     b. Cliquez avec le bouton droit sur le certificat téléchargé dans le portail Azure, puis ouvrez le fichier dans le Bloc-notes ou tout autre éditeur de texte. Copiez le texte situé entre le début et la fin du titre du certificat, puis collez-le dans la zone de texte **Certificat X.509**.

9. Cliquez sur **Save**. Consultez le [Guide Asana pour configurer l’authentification unique](https://asana.com/guide/help/premium/authentication#gl-saml) si vous avez besoin d’aide.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Bouton Ajouter](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-an-asana-test-user"></a>Créer un utilisateur de test Asana

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Asana.

1. Dans **Asana**, accédez à la section **Teams** (Équipes) dans le volet gauche. Cliquez sur le signe plus. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Tapez le message électronique britta.simon@contoso.com dans la zone de texte, puis sélectionnez **Invite**(Inviter).

3. Cliquez sur **Send Invite**(Envoyer l’invitation). Le nouvel utilisateur recevra un message électronique dans sa messagerie. Elle devra créer le compte et le valider.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Asana.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Asana, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Asana**.

    ![Lien Asana dans la liste des applications](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD.

Accédez à la page de connexion à Asana. Dans la zone correspondante, insérez l’adresse e-mail britta.simon@contoso.com. Laissez la zone du mot de passe vide, puis cliquez sur **Connexion**. Vous êtes redirigé vers la page de connexion à Azure AD. Entrez vos informations d’identification Azure AD. Vous êtes maintenant connecté à Asana.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
