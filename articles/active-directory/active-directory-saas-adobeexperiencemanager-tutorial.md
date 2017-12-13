---
title: "Didacticiel : Intégration d’Azure Active Directory à Adobe Experience Manager | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Experience Manager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4ba94f0f24b2791b3b32807aa60379aeadb79365
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Experience Manager

L’objectif de ce didacticiel est de vous apprendre à intégrer Adobe Experience Manager à Azure Active Directory (Azure AD).

L’intégration d’Adobe Experience Manager à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Experience Manager.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adobe Experience Manager (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Adobe Experience Manager, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à Adobe Experience Manager pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Adobe Experience Manager à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Ajout d’Adobe Experience Manager à partir de la galerie
Pour configurer l’intégration d’Adobe Experience Manager à Azure AD, vous devez ajouter Adobe Experience Manager à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Adobe Experience Manager à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Adobe Experience Manager**, sélectionnez **Adobe Experience Manager** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Adobe Experience Manager dans la liste des résultats](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec Adobe Experience Manager, via un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur Adobe Experience Manager équivaut à un utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Adobe Experience Manager associé doit être établie.

Dans Adobe Experience Manager, affectez la valeur de **nom d’utilisateur** d’Azure AD comme valeur de **nom d’utilisateur**, afin d’établir la relation.

Pour configurer et tester l’authentification unique d’Azure AD avec Adobe Experience Manager, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Adobe Experience Manager](#create-an-adobe-experience-manager-test-user)** pour avoir un équivalent de Britta Simon dans Adobe Experience Manager, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure, et configurer l’authentification unique dans votre application Adobe Experience Manager.

**Pour configurer l’authentification unique Azure AD avec Adobe Experience Manager, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Adobe Experience Manager**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Dans la section **Adobe Experience Manager Domain and URLs** (Domaines et URL Adobe Experience Manager), suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode **IdP** :

    ![Informations d’authentification unique dans Adobe Experience Manager Domain and URLs (Domaines et URL Adobe Experience Manager)](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Dans la zone de texte **Identificateur**, saisissez une valeur unique que vous définissez sur votre serveur AEM également. 

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<AEM Server Url>/saml_login`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Si vous souhaitez configurer l’application en mode démarré par le **fournisseur de services**, cochez Afficher les paramètres d’URL avancés, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Adobe Experience Manager Domain and URLs (Domaines et URL Adobe Experience Manager)](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Dans la zone de texte **URL de connexion**, saisissez l’URL de votre serveur Adobe Experience Manager. 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Dans la section relative à la configuration d’Adobe Experience Manager, cliquez sur le bouton permettant de configurer Adobe Experience Manager pour ouvrir la fenêtre sur la configuration de l’authentification unique. Copiez **l’URL de service de connexion SAML**, **l’ID d’entité SAML** et **l’ID de déconnexion** à partir de la section Référence rapide.

    ![Lien Section de configuration](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Ouvrez le portail d’administration **Adobe Experience Manager** dans une autre fenêtre de navigateur.

9. Sélectionnez **Paramètres** -> **Sécurité** -> **Utilisateurs**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Sélectionnez **Administrateur** ou tout autre utilisateur approprié.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Sélectionnez **Paramètres du compte** -> **Create/Manage TrustStore** (Créer/gérer TrustStore).

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Cliquez sur **Sélectionner un fichier de certificat** à partir du bouton **Add Certificate from CER file** (Ajouter un certificat à partir d’un fichier CER). Accédez au fichier de certificat que vous avez téléchargé à partir du portail Azure et sélectionnez-le.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Le certificat est ajouté au TrustStore. Notez l’alias du certificat.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Sur la page **Utilisateurs**, sélectionnez **authentication-service**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Sélectionnez **Paramètres du compte** -> **Create/Manage KeyStore** (Créer/gérer KeyStore). Créez KeyStore en fournissant un mot de passe.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Revenez à l’écran d’administration et sélectionnez **Paramètres** -> **Operations** -> **Console Web**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. La page de configuration s’ouvre.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. Rechercher **Adobe Granite SAML 2.0 Authentication Handler**, puis cliquez sur l’icône **Ajouter**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Sur cette page, effectuez les actions suivantes.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Dans la zone de texte **Chemin d’accès**, entrez **/**.

    b. Dans la zone de texte **IDP URL** (URL d’IDP), collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **IDP Certificate Alias** (Alias de certificat IDP), entrez la valeur de **Certificate Alias** (Alias de certificat) que vous avez ajoutée dans TrustStore.

    d. Dans la zone de texte **Security Provided Entity ID** (ID d’entité fourni par la sécurité), entrez la valeur **d’ID d’entité SAML** unique que vous avez configurée dans le portail Azure.

    e. Dans la zone de texte **Assertion Consumer Service URL**, entrez la valeur **d’URL de réponse** que vous avez configurée dans le portail Azure.

    f. Dans la zone de texte **Password of Key Store** (Mot de passe de KeyStore), entrez le **mot de passe** que vous avez défini dans KeyStore.

    g. Dans la zone de texte **User Attribute ID** (ID d’attribut utilisateur), entrez **l’ID de nom** ou un autre ID d’utilisateur pertinent.

    h. Sélectionnez **Autocreate CRX Users** (Créer automatiquement des utilisateurs CRX).

    i. Dans la zone de texte **URL de déconnexion**, collez la valeur **d’URL de déconnexion** unique que vous avez copiée à partir du portail Azure.

    j. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Créer un utilisateur de test Adobe Experience Manager

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Adobe Experience Manager. Si vous avez sélectionné l’option **Autocreate CRX Users** (Créer automatiquement des utilisateurs CRX), les utilisateurs sont créés automatiquement après une authentification réussie. 

Si vous souhaitez créer manuellement des utilisateurs, collaborez avec [l’équipe de support technique Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) pour ajouter les utilisateurs dans la plateforme Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Experience Manager.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Adobe Experience Manager, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Adobe Experience Manager**.

    ![Lien Adobe Experience Manager dans la liste des applications](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Adobe Experience Manager dans le volet d’accès, vous devez être connecté automatiquement à votre application Adobe Experience Manager.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

