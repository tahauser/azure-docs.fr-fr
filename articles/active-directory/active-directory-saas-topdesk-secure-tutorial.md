---
title: "Didacticiel : Intégration d’Azure Active Directory à TOPdesk - Secure | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et TOPdesk - Secure."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: ca3362bc3f966adaf9940f6eb4bec5235c6ea7d8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Didacticiel : Intégration d’Azure Active Directory à TOPdesk - Secure

Dans ce didacticiel, vous allez apprendre à intégrer TOPdesk - Secure à Azure Active Directory (Azure AD).

L’intégration de TOPdesk - Secure à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TOPdesk - Secure.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à TOPdesk - Secure (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à TOPdesk - Secure, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TOPdesk - Secure pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de TOPdesk - Secure à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-topdesk---secure-from-the-gallery"></a>Ajout de TOPdesk - Secure à partir de la galerie
Pour configurer l’intégration de TOPdesk - Secure à Azure AD, vous devez ajouter TOPdesk - Secure à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter TOPdesk - Secure à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **TOPdesk - Secure**, sélectionnez **TOPdesk - Secure** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![TOPdesk - Secure dans la liste des résultats](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TOPdesk - Secure avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TOPdesk - Secure équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur TOPdesk - Secure associé doit être établie.

Dans TOPdesk - Secure, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec TOPdesk - Secure, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test TOPdesk - Secure](#create-a-topdesk---secure-test-user)** pour avoir un équivalent de Britta Simon dans TOPdesk - Secure lié à la représentation de l’utilisateur dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application TOPdesk - Secure.

**Pour configurer l’authentification unique Azure AD avec TOPdesk - Secure, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **TOPdesk - Secure**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. Dans la section **Domaine et URL TOPdesk - Secure**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.topdesk.net`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. L’URL de réponse est expliquée plus loin dans le didacticiel. Contactez l’[équipe de support technique TOPdesk - Secure](http://www.topdesk.com/us/support) pour obtenir ces valeurs. 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de TOPdesk - Secure**, cliquez sur **Configurer TOPdesk - Secure** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Connectez-vous à votre site d’entreprise **TOPdesk - Secure** en tant qu’administrateur.

8. Dans le menu **TOPdesk**, cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-topdesk-secure-tutorial/ic790598.png "Paramètres")

9. Cliquez sur **Login Settings**.

    ![Paramètres de connexion](./media/active-directory-saas-topdesk-secure-tutorial/ic790599.png "Paramètres de connexion")

10. Développez le menu **Login Settings**, puis cliquez sur **General**.

    ![Général](./media/active-directory-saas-topdesk-secure-tutorial/ic790600.png "Général")

11. Dans la section **Secure** de la section de configuration **SAML login**, procédez comme suit :

    ![Paramètres techniques](./media/active-directory-saas-topdesk-secure-tutorial/ic790855.png "Paramètres techniques")
   
    a. Cliquez sur **Download** pour télécharger le fichier de métadonnées public et enregistrez-le en local sur votre ordinateur.
   
    b. Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService**.
    
    ![Assertion Consumer Service](./media/active-directory-saas-topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")
   
    c. Copiez la valeur **AssertionConsumerService**, collez-la dans la zone de texte URL de réponse dans la section **Domaine et URL TOPdesk - Secure**.

12. Pour créer un fichier de certificat, procédez comme suit :
    
    ![Certificat](./media/active-directory-saas-topdesk-secure-tutorial/ic790606.png "Certificat")
    
    a. Ouvrez le fichier de métadonnées téléchargé à partir du portail Azure.

    b. Développez le nœud **RoleDescriptor** dont le **xsi:type** est **fed:ApplicationServiceType**.

    c. Copiez la valeur du nœud **X509Certificate** .

    d. Enregistrez la valeur de **X509Certificate** copiée, dans un fichier local sur votre ordinateur.

13. Dans la section **Public**, cliquez sur **Add**.
    
    ![Add](./media/active-directory-saas-topdesk-secure-tutorial/ic790607.png "Add")

14. Dans la boîte de dialogue **SAML configuration assistant** , procédez comme suit :
    
    ![Assistant de configuration SAML](./media/active-directory-saas-topdesk-secure-tutorial/ic790608.png "Assistant de configuration SAML")
    
    a. Pour charger votre fichier de métadonnées téléchargé à partir du portail Azure, dans **Métadonnées de fédération**, cliquez sur **Parcourir**.

    b. Pour charger votre fichier de certificat, sous **Certificate (RSA)**, cliquez sur **Browse**.

    c. Pour charger le fichier de logo que vous avez obtenu de l’équipe de support TOPdesk, sous **Logo icon**, cliquez sur **Browse**.

    d. Dans la zone de texte **User name attribute** (Attribut de nom d’utilisateur), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Dans la zone de texte **Display name** , indiquez le nom de votre configuration.

    f. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-topdesk---secure-test-user"></a>Créer un utilisateur de test TOPdesk - Secure

Pour se connecter à TOPdesk - Secure, les utilisateurs d’Azure AD doivent être approvisionnés dans TOPdesk - Secure.  
Dans le cas de TOPdesk - Secure, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **TOPdesk - Secure** en tant qu’administrateur.
2. Dans le menu en haut, cliquez sur **TOPdesk \> New \> Support Files \> Operator**.
   
    ![Operator (Opérateur)](./media/active-directory-saas-topdesk-secure-tutorial/ic790610.png "Operator (Opérateur)")

3. Dans la boîte de dialogue **New Operator** , procédez comme suit :
   
    ![New Operator (Nouvel opérateur)](./media/active-directory-saas-topdesk-secure-tutorial/ic790611.png "New Operator (Nouvel opérateur)")
   
    a. Cliquez sur l’onglet **General** (Général).
   
    b. Dans la zone de texte **Surname** (Nom), tapez le nom de l’utilisateur, par exemple **Simon**.
   
    c. Sélectionnez un **Site** pour le compte dans la section **Emplacement**.
   
    d. Dans la zone de texte **Login Name** de la section **TOPdesk Login**, indiquez le nom de connexion de votre utilisateur.
   
    e. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par TOPdesk - Secure, pour approvisionner des comptes d’utilisateur AAD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TOPdesk - Secure.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à TOPdesk - Secure, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **TOPdesk - Secure**.

    ![Lien TOPdesk - Secure dans la liste des applications](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette TOPdesk - Secure dans le volet d’accès, vous devez être connecté automatiquement à votre application TOPdesk - Secure.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_203.png

