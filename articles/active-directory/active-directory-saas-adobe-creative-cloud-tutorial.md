---
title: "Didacticiel : Intégration d’Azure Active Directory à Adobe Creative Cloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: c34d6bddb733c5979bc2006738e950cf7a185c4e
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Creative Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Adobe Creative Cloud à Azure Active Directory (Azure AD).

L’intégration de Adobe Creative Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Creative Cloud.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adobe Creative Cloud (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Adobe Creative Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adobe Creative Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Adobe Creative Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Ajout de Adobe Creative Cloud à partir de la galerie
Pour configurer l’intégration de Adobe Creative Cloud à Azure AD, vous devez ajouter Adobe Creative Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Adobe Creative Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Adobe Creative Cloud**, sélectionnez **Adobe Creative Cloud** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Adobe Creative Cloud dans la liste des résultats](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adobe Creative Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Adobe Creative Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Adobe Creative Cloud associé doit être établie.

Dans Adobe Creative Cloud, attribuez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique avec Azure AD avec Adobe Creative Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Adobe Creative Cloud lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Adobe Creative Cloud.

**Pour configurer l’authentification unique Azure AD avec Adobe Creative Cloud, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Adobe Creative Cloud**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Dans la section **Domaines et URL Adobe Creative Cloud**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par IDP :

    ![Informations d’authentification unique dans Domaine et URL Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<token>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique d’Adobe Creative Cloud](https://helpx.adobe.com/in/contact/support.html). 

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur suivante : `https://adobe.com`

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. L’application Adobe Creative Cloud attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attributs utilisateur** de l’application. La capture d’écran suivante montre un exemple :

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| ----------------|
    | FirstName |user.givenname |
    | LastName |user.surname |
    | Email |user.mail |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.
    
8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. Dans la section **Configuration de Adobe Creative Cloud**, cliquez sur **Configurer Adobe Creative Cloud** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. Dans une fenêtre différente de navigateur, connectez-vous à [Adobe Admin Console](https://adminconsole.adobe.com) en tant qu’administrateur.

11. Accédez à **Paramètres** sur la barre de navigation supérieure, puis sélectionnez **Identité**. La liste des domaines s’ouvre. Cliquez sur le lien **Configurer** en regard de votre domaine. Suivez ensuite la procédure de la section **Configuration de l’authentification unique requise**. Pour plus d’informations, consultez la section relative à la [configuration d’un domaine](https://helpx.adobe.com/enterprise/using/set-up-domain.html).

    ![Paramètres](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Paramètres")
    
    a. Cliquez sur **Parcourir** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat IDP**.
    
    b. Dans la zone de texte **IDP issuer**, placez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis la section **Configurer l’authentification** du portail Azure.
    
    c. Dans la zone de texte **IDP Login URL**, placez la valeur de **l’URL du service SSO SAML** que vous avez copiée depuis la section **Configurer l’authentification** du portail Azure.
    
    d. Sélectionnez **HTTP - Redirection** en tant que **Liaison IDP**.
    
    e. Sélectionnez **Adresse de messagerie** en tant que **Paramètre de connexion utilisateur**.
    
    f. Cliquez sur le bouton **Enregistrer** .

12. Le tableau de bord présente maintenant le fichier XML **« Télécharger les métadonnées »**. Il contient les URL EntityDescriptor et AssertionConsumerService d’Adobe. Ouvrez le fichier et configurez-les dans l’application Azure AD.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilisez la valeur EntityDescriptor que Adobe vous a fournie pour **Identificateur** dans la boîte de dialogue **Configurer les paramètres d’application**.

    b. Utilisez la valeur AssertionConsumerService que Adobe vous a fournie pour **URL de réponse** dans la boîte de dialogue **Configurer les paramètres d’application**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Créer un utilisateur de test Adobe Creative Cloud

Pour permettre aux utilisateurs Azure AD de se connecter à Adobe Creative Cloud, vous devez les approvisionner dans Adobe Creative Cloud. Dans le cas d’Adobe Creative Cloud, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous au site [Adobe Admin Console](https://adminconsole.adobe.com) en tant qu’administrateur.

2. Ajoutez l’utilisateur au sein de la console Adobe en tant qu’ID fédéré et attribuez-le à un profil de produits. Pour plus d’informations sur l’ajout des utilisateurs, consultez la section [Ajouter des utilisateurs dans Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers). 

3. À ce stade, tapez votre adresse de messagerie/nom d’utilisateur principal dans le formulaire de connexion Adobe, appuyez sur la touche de tabulation, et vous devez être fédéré à Azure AD :
    * Accès Web : www.adobe.com > connexion
    * Dans l’utilitaire de l’application de bureau > connexion
    * Dans l’application > aide > connexion

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Creative Cloud.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon Adobe Creative Cloud, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Adobe Creative Cloud**.

    ![Lien Adobe Creative Cloud dans la liste Applications](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Adobe Creative Cloud dans le volet d’accès, vous devez être connecté automatiquement à votre application Adobe Creative Cloud.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer un domaine (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Configurer Azure pour une utilisation avec l’authentification unique Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

