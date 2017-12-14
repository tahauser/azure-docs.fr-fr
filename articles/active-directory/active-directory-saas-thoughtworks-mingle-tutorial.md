---
title: "Didacticiel : intégration d’Azure Active Directory à Thoughtworks Mingle | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Thoughtworks Mingle."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 4406524f7ba85c33c0e309d3cc7cd21e2f5e327b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Didacticiel : Intégration d’Azure AD à Thoughtworks Mingle

Dans ce didacticiel, vous allez apprendre à intégrer Thoughtworks Mingle à Azure Active Directory (Azure AD).

L’intégration de Thoughtworks Mingle à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Thoughtworks Mingle.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Thoughtworks Mingle (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Thoughtworks Mingle, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Thoughtworks Mingle pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Thoughtworks Mingle à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Ajout de Thoughtworks Mingle à partir de la galerie
Pour configurer l’intégration de Thoughtworks Mingle à Azure AD, vous devez ajouter Thoughtworks Mingle depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Thoughtworks Mingle à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Thoughtworks Mingle**, sélectionnez **Thoughtworks Mingle** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Thoughtworks Mingle dans la liste des résultats](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Thoughtworks Mingle avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur de Thoughtworks Mingle équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur de Thoughtworks Mingle associé doit être établie.

Dans Thoughtworks Mingle, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Thoughtworks Mingle, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)** pour avoir un équivalent de Britta Simon dans Thoughtworks Mingle lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Thoughtworks Mingle.

**Pour configurer l’authentification unique Azure AD avec Thoughtworks Mingle, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Thoughtworks Mingle**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. Dans la section **Domaine et URL Thoughtworks Mingle**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique Thoughtworks Mingle](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Connectez-vous à votre site d’entreprise **Thoughtworks Mingle** en tant qu’administrateur.

7. Cliquez sur l’onglet **Admin**, puis sur **SSO Config (Configuration SSO)**.
   
    ![Onglet Admin](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785157.png "Configuration SSO")

8. Dans la section **SSO Config** , procédez comme suit :
   
    ![Configuration de l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785158.png "Configuration de l’authentification unique")
    
    a. Pour charger le fichier de métadonnées, cliquez sur **Choose file**. 

    b. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Bouton Ajouter](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Créer un utilisateur de test Thoughtworks Mingle

Pour que les utilisateurs Azure AD puissent se connecter, ils doivent être approvisionnés dans l’application Thoughtworks Mingle à l’aide de leurs noms d’utilisateur Azure AD. Dans le cas de Thoughtworks Mingle, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Thoughtworks Mingle en tant qu’administrateur.

2. Cliquez sur **Profile**.
   
    ![Votre premier projet](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785160.png "Votre premier projet")

3. Cliquez sur l’onglet **Admin**, puis sur **Users (Utilisateurs)**.
   
    ![Utilisateurs](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785161.png "Utilisateurs")

4. Cliquez sur **New User**.
   
    ![Nouvel utilisateur](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785162.png "Nouvel utilisateur")

5. Dans la boîte de dialogue **New User** , procédez comme suit :
   
    ![Boîte de dialogue Nouvel utilisateur](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785163.png "Nouvel utilisateur")  
 
    a. Renseignez les zones de texte **Sign-in name** (Nom de connexion), **Display name** (Nom d’affichage), **Choose password** (Choisir le mot de passe) et **Confirm password** (Confirmer le mot de passe) du compte Azure AD valide que vous souhaitez approvisionner. 

    b. Dans **User type (Type d’utilisateur)**, sélectionnez **Full user (Utilisateur complet)**.

    c. Cliquez sur **Create This Profile**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par Thoughtworks Mingle, pour approvisionner des comptes d’utilisateur AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Thoughtworks Mingle.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Thoughtworks Mingle, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Thoughtworks Mingle**.

    ![Lien Thoughtworks Mingle dans la liste des applications](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette Thoughtworks Mingledans le volet d’accès, vous devez vous connecter automatiquement à votre application Thoughtworks Mingle.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_203.png

