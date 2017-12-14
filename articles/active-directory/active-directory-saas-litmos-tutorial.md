---
title: "Didacticiel : Intégration d’Azure Active Directory avec Litmos | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Litmos."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 2447cc5dbb01aa718e4418386499c6e50e9f5184
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Didacticiel : Intégration d’Azure Active Directory avec Litmos

Dans ce didacticiel, vous allez apprendre à intégrer Litmos avec Azure Active Directory (Azure AD).

L’intégration de Litmos dans Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Litmos.
- Vous pouvez permettre à vos utilisateurs d’être automatiquement connectés à Litmos (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Litmos, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Litmos pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Litmos à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-litmos-from-the-gallery"></a>Ajout de Litmos à partir de la galerie
Pour configurer l’intégration de Litmos avec Azure AD, vous devez ajouter Litmos à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Litmos à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Litmos**, sélectionnez **Litmos** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Litmos dans la liste des résultats](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Litmos sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Litmos équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Litmos associé doit être établie.

Dans Litmos, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec Litmos, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Litmos](#create-a-litmos-test-user)** pour avoir un équivalent de Britta Simon dans Litmos lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Litmos.

**Pour configurer l’authentification unique Azure AD avec Litmos, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Litmos**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_samlbase.png)

3. Dans la section **Domaine et URL Litmos**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Litmos](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.litmos.com/account/Login`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réelles qui sont décrits plus loin dans le didacticiel, ou contactez l’[équipe de support technique Litmos](https://www.litmos.com/contact-us/) pour obtenir ces valeurs.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_certificate.png)

5. Dans le cadre de la configuration, vous devez personnaliser les **Attributs du jeton SAML** pour votre application Litmos.

    ![Section de l’attribut](./media/active-directory-saas-litmos-tutorial/tutorial_attribute.png)
           
    | Nom de l'attribut   | Valeur de l’attribut |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | LastName  |user.surname |
    | Email |user.mail |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Ajouter un attribut](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_04.png)

    ![Boîte de dialogue Ajouter un attribut](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.     

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de Configurer l’authentification unique](./media/active-directory-saas-litmos-tutorial/tutorial_general_400.png)

7. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Litmos en tant qu’administrateur.

8. Dans la barre de navigation située à gauche, cliquez sur **Accounts**.
   
    ![Section Comptes côté application][22] 

9. Cliquez sur l’onglet **Integrations** .
   
    ![Onglet Intégration][23] 

10. Sur l’onglet **Integrations**, accédez à **3rd Party Integrations**, puis cliquez sur l’onglet **SAML 2.0**.
   
    ![Section SAML 2.0][24] 

11. Copiez la valeur sous **The SAML endpoint for litmos is** (Le point de terminaison SAML pour litmos est), puis collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Litmos** du portail Azure. 
   
    ![Point de terminaison SAML][26] 

12. Dans votre application **Litmos** , procédez comme suit :
    
     ![Application Litmos][25] 
     
     a. Cliquez sur **Enable SAML**.
    
     b. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **SAML X.509 Certificate** .
     
     c. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-litmos-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
  
### <a name="create-a-litmos-test-user"></a>Créer un utilisateur de test Litmos

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Litmos.  
L’application Litmos prend en charge l’approvisionnement juste-à-temps. Cela signifie qu’un compte d’utilisateur est automatiquement créé si nécessaire pendant la tentative d’accès à l’application à l’aide du volet d’accès.

**Pour créer un utilisateur appelé Britta Simon dans Litmos, procédez comme suit :**

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Litmos en tant qu’administrateur.

2. Dans la barre de navigation située à gauche, cliquez sur **Accounts**.
   
    ![Section Comptes côté application][22] 

3. Cliquez sur l’onglet **Integrations** .
   
    ![Onglet Intégrations][23] 

4. Sur l’onglet **Integrations**, accédez à **3rd Party Integrations**, puis cliquez sur l’onglet **SAML 2.0**.
   
    ![SAML 2.0][24] 
    
5. Sélectionner **Générer automatiquement les utilisateurs**
   
    ![Générer automatiquement les utilisateurs][27] 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Litmos.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Litmos, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Litmos**.

    ![Lien Litmos dans la liste des applications](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Lorsque vous cliquez sur la vignette Litmos dans le volet d’accès, vous devez être connecté automatiquement à votre application Litmos. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png

