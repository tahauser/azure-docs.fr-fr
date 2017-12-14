---
title: "Didacticiel : Intégration d’Azure Active Directory à Zoho | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zoho."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 51a10a4d687a0d218b5439ebf5aeb1d0895c6161
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Didacticiel : Intégration d’Azure Active Directory à Zoho

Dans ce didacticiel, vous allez apprendre à intégrer Zoho dans Azure Active Directory (Azure AD).

L’intégration de Zoho dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Zoho.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Zoho (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Zoho, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Zoho pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Zoho à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zoho-from-the-gallery"></a>Ajout de Zoho à partir de la galerie
Pour configurer l’intégration de Zoho avec Azure AD, vous devez ajouter Zoho, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zoho à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Zoho**, sélectionnez **Zoho** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Zoho dans la liste des résultats](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zoho avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Zoho équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Zoho associé doit être établie.

Dans Zoho, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Zoho, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Zoho](#create-a-zoho-test-user)** pour avoir dans Zoho un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Zoho.

**Pour configurer l’authentification unique Azure AD avec Zoho, effectuez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Zoho**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. Dans la section **Domaine et URL Zoho**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support Zoho](https://www.zoho.com/mail/contact.html). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Zoho**, cliquez sur **Configurer Zoho** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’URL de modification du mot de passe et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoho Mail en tant qu’administrateur.

8. Accédez à **Control panel**.
   
    ![Panneau de configuration](./media/active-directory-saas-zoho-mail-tutorial/ic789607.png "Panneau de configuration")

9. Cliquez sur l’onglet **SAML Authentication** .
   
    ![Authentification SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789608.png "Authentification SAML")

10. Dans la section **SAML Authentication Details** , procédez comme suit :
   
    ![Détails d’authentification SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789609.png "Détails d’authentification SAML")
   
    a. Dans la zone de texte **Login URL**, collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    b. Dans la zone de texte **Logout URL**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
   
    c. Dans la zone de texte **Change Password Link**, collez l’**URL de modification de mot de passe** que vous avez copié à partir du portail Azure.
       
    d. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PublicKey**.
   
    e. Comme **Algorithme**, sélectionnez **RSA**.
   
    f. Cliquez sur **OK**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-zoho-test-user"></a>Créer un utilisateur de test Zoho

Pour que les utilisateurs d’Azure AD puissent se connecter à Zoho Mail, ils doivent être approvisionnés dans Zoho Mail. Dans le cas de Zoho Mail, l’approvisionnement est une tâche manuelle.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zoho Mail pour approvisionner des comptes d’utilisateurs Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise **Zoho Mail** en tant qu’administrateur.

2. Accédez à **Control Panel \> Mail & Docs**.

3. Accédez à **User Details \> Add User**.
   
    ![Ajouter un utilisateur](./media/active-directory-saas-zoho-mail-tutorial/ic789611.png "Ajouter un utilisateur")

4. Dans la boîte de dialogue **Add users** , procédez comme suit :
   
    ![Ajouter un utilisateur](./media/active-directory-saas-zoho-mail-tutorial/ic789612.png "Ajouter un utilisateur")
   
    a. Dans la zone de texte **First Name**, tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Email**, tapez l’ID d’e-mail d’un utilisateur, par exemple, **brittasimon@contoso.com**.

    d. Dans la zone de texte **Password**, tapez le mot de passe de l’utilisateur.
   
    e. Cliquez sur **OK**.  
      
    > [!NOTE]
    > Le titulaire du compte Azure AD reçoit un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zoho.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Zoho, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Zoho**.

    ![Lien Zoho dans la liste des applications](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Zoho dans le volet d’accès, vous devez être connecté automatiquement à votre application Zoho.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_203.png

