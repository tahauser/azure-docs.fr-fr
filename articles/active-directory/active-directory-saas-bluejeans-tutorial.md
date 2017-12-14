---
title: "Didacticiel : Intégration d’Azure Active Directory à BlueJeans | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et BlueJeans."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: jeedes
ms.openlocfilehash: 828149bee642cf67e34255924ace2c37bdf0e320
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Didacticiel : Intégration d’Azure Active Directory à BlueJeans

Dans ce didacticiel, vous allez apprendre à intégrer BlueJeans à Azure Active Directory (Azure AD).

L’intégration de BlueJeans à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à BlueJeans.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à BlueJeans (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à BlueJeans, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement BlueJeans pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de BlueJeans à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-bluejeans-from-the-gallery"></a>Ajout de BlueJeans à partir de la galerie
Pour configurer l’intégration de BlueJeans à Azure AD, vous devez ajouter BlueJeans à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter BlueJeans à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **BlueJeans**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_search.png)

5. Dans le volet de résultats, sélectionnez **BlueJeans**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec BlueJeans avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur BlueJeans correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur BlueJeans associé.

Dans BlueJeans, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec BlueJeans, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test BlueJeans](#creating-a-bluejeans-test-user)** pour avoir un équivalent de Britta Simon dans BlueJeans lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application BlueJeans.

**Pour configurer l’authentification unique Azure AD avec BlueJeans, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **BlueJeans**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. Dans la section **Domaine et URL BlueJeans**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.BlueJeans.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.BlueJeans.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support BlueJeans](https://support.bluejeans.com/contact). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de BlueJeans**, cliquez sur **Configurer BlueJeans** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’URL de modification du mot de passe et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

8. Accédez à **ADMIN \> Group Settings \> Security**.
   
   ![Administrateur](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administrateur")

9. Dans la section **Security** , procédez comme suit :
   
   ![Authentification unique SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Authentification unique SAML")   
   
   a. Sélectionnez **SAML Single Sign On**.
  
   b. Sélectionnez **Enable automatic provisioning**.

10. Poursuivez en procédant comme suit :

    ![Chemin d’accès du certificat](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Chemin d’accès du certificat")
    
    a. Cliquez sur **Choose File**, puis chargez le certificat téléchargé.
   
    b. Collez la valeur du champ **URL du service d’authentification unique SAML** dans la zone de texte **Login URL** (URL de connexion).
   
    c. Collez la valeur du champ **Modifier l’URL de mot de passe** dans la zone de texte **Password Change URL** (URL de modification du mot de passe).
   
    d. Collez la valeur du champ **URL de déconnexion** dans la zone de texte **Logout URL** (URL de déconnexion).

11. Poursuivez en procédant comme suit :
    
    ![Enregistrer les modifications](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Enregistrer les modifications")
    
    a. Dans la zone de texte **User ID** (ID utilisateur), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    b. Dans la zone de texte **Email**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    c. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-bluejeans-test-user"></a>Création d’un utilisateur de test BlueJeans

Pour permettre aux utilisateurs Azure AD de se connecter à BlueJeans, vous devez les attribuer dans BlueJeans.  

Pour BlueJeans, cet approvisionnement doit se faire manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

2. Accédez à **ADMIN \> Manage Users \> Add User**.
   
   ![Administrateur](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administrateur")
   
   >[!IMPORTANT]
   >L’onglet **Add User** est disponible uniquement si, sous l’onglet **Security**, l’option **Enable automatic provisioning** est décochée. 
   
3. Dans la section **Add User** , procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Ajouter un utilisateur")
    
    a. Tapez un **nom d’utilisateur BlueJeans**, une **adresse de messagerie**, un **ID de réunion BlueJeans**, un **code secret de modérateur**, un **nom complet** et la **société** d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
    
    b. Cliquez sur **Add User**.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par BlueJeans pour approvisionner des comptes d’utilisateur Azure Active Directory. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BlueJeans.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à BlueJeans, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **BlueJeans**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette BlueJeans dans le panneau d’accès, la page de connexion de l’application BlueJeans doit s’afficher.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_203.png

