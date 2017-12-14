---
title: "Didacticiel : Intégration d’Azure Active Directory dans Lifesize Cloud | Documentation Microsoft"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lifesize Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 47ae2c59d672c00b466d13aad1d304f4669a38c7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à Lifesize Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Lifesize Cloud à Azure Active Directory (Azure AD).

L’intégration de Lifesize Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Lifesize Cloud
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Lifesize Cloud (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Lifesize Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Lifesize Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Lifesize Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Ajout de Lifesize Cloud à partir de la galerie
Pour configurer l’intégration de Lifesize Cloud à Azure AD, vous devez ajouter Lifesize Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Lifesize Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Lifesize Cloud**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. Dans le panneau de résultats, sélectionnez **Lifesize Cloud**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Lifesize Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Lifesize Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Lifesize Cloud associé doit être établie.

Dans Lifesize Cloud, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique avec Azure AD avec Lifesize Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Lifesize Cloud](#creating-a-lifesize-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Lifesize Cloud qui est lié à la représentation d’un utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Lifesize Cloud.

**Pour configurer l’authentification unique Azure AD avec Lifesize Cloud, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Lifesize Cloud**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. Dans la section **Domaine et URL Lifesize Cloud**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://login.lifesizecloud.com/ls/?acs`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://login.lifesizecloud.com/<companyname>`

     
4. Cochez l’option **Afficher les paramètres d’URL avancés**, puis procédez comme suit :    
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    Dans la zone de texte **État de relais**, entrez une valeur en respectant le format suivant : `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’état de relais et l’identificateur réels. Pour obtenir les valeurs d’URL de connexion et d’identificateur, contactez [l’équipe de support aux clients Lifesize Cloud](https://www.lifesize.com/support). Vous pouvez également obtenir la valeur de l’état de relais à partir de la configuration SSO, expliquée plus loin dans ce didacticiel.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration Lifesize Cloud**, cliquez sur **Configurer Lifesize Cloud**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Pour configurer SSO pour votre application, connectez-vous à l’application Lifesize Cloud avec les privilèges d’administrateur.

8. Dans le coin supérieur droit, cliquez sur votre nom, puis cliquez sur **Paramètres avancés**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. Dans les Paramètres avancés, cliquez maintenant sur le lien **Configuration de l’authentification unique**. La page Configuration SSO de l’instance s’ouvre.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Maintenant, configurez les valeurs suivantes dans l’interface utilisateur de configuration de l’authentification unique.    
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. Dans la zone de texte **Émetteur du fournisseur d'identité**, collez la valeur de l’**ID d’entité SAML** copié à partir du portail Azure.

    b.  Dans la zone de texte **URL de connexion**, collez la valeur de l’**URL du service d’authentification unique SAML** copiée à partir du portail Azure.

    c. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat X.509**.
  
    d. Dans le mappage d’attribut SAML pour la zone de texte Prénom, saisissez la valeur **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. Dans le mappage d’attribut SAML pour la zone de texte **Nom**, saisissez la valeur **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. Dans le mappage d’attribut SAML pour la zone de texte **E-mail**, saisissez la valeur **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Pour vérifier la configuration, vous pouvez cliquer sur le bouton **Test**.
   
    >[!NOTE]
    >Pour que les tests se déroulent correctement, vous devez exécuter l’assistant de configuration dans Azure AD et également fournir l’accès aux utilisateurs ou groupes autorisés à effectuer le test.

12. Activez l’authentification unique en appuyant sur le bouton **Activer l’authentification unique**.

13. Maintenant, cliquez sur le bouton **Mettre à jour** afin que tous les paramètres soient enregistrés. Cela génère la valeur RelayState. Copiez la valeur RelayState générée dans la zone de texte, puis collez-la dans la zone de texte **État de relais**, sous la section **Domaine et URL Lifesize Cloud**. 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Créer un utilisateur de test Lifesize Cloud

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Lifesize Cloud. Lifesize Cloud ne prend pas en charge l’approvisionnement automatique des utilisateurs. Après une authentification réussie à Azure AD, l’utilisateur sera automatiquement approvisionné dans l’application. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Lifesize Cloud.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Lifesize Cloud, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Lifesize Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette Lifesize Cloud dans le Panneau d’accès, vous allez en principe être connecté automatiquement à votre application Lifesize Cloud.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

