---
title: "Didacticiel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 9fc4666ad73a7e4850719f936873a6c5ce0716c0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Didacticiel : Intégration d’Azure Active Directory avec DocuSign

Dans ce didacticiel, vous allez apprendre à intégrer DocuSign à Azure Active Directory (Azure AD).

L’intégration de DocuSign à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à DocuSign.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à DocuSign (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec DocuSign, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement DocuSign pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de DocuSign à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-docusign-from-the-gallery"></a>Ajout de DocuSign à partir de la galerie
Pour configurer l’intégration de DocuSign à Azure AD, vous devez ajouter DocuSign à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter DocuSign à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **DocuSign**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. Dans le volet de résultats, sélectionnez **DocuSign**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec DocuSign, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur DocuSign correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur associé dans DocuSign doit être établie.

Pour cela, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans DocuSign.

Pour configurer et tester l’authentification unique Azure AD avec DocuSign, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur test DocuSign](#creating-a-docusign-test-user)** pour avoir un équivalent de Britta Simon dans DocuSign lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application DocuSign.

**Pour configurer l’authentification unique Azure AD avec DocuSign, effectuez les étapes suivantes :**

1. Sur le portail Azure, à la page d’intégration de l’application **DocuSign**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. Dans la section **Configuration de DocuSign** du portail Azure, cliquez sur **Configurer DocuSign** pour ouvrir la fenêtre Configurer l’authentification. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**
    
    ![Configurer l’authentification unique](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration DocuSign** en tant qu’administrateur.

6. Dans le menu de navigation à gauche, cliquez sur **Domaines**.
   
    ![Configuration de l'authentification unique][51]

7. Dans le volet droit, cliquez sur **Claim Domain**(Revendiquer un domaine).
   
    ![Configuration de l'authentification unique][52]

8. Dans la boîte de dialogue **Claim a domain** (Revendiquer un domaine), dans la zone de texte **Nom du domaine**, indiquez votre domaine d’entreprise, puis cliquez sur **Revendication**. Veillez à vérifier le domaine et assurez-vous que son état est actif.
   
    ![Configuration de l'authentification unique][53]

9. Dans le menu de gauche, cliquez sur **Fournisseurs d’identité**  
   
    ![Configuration de l'authentification unique][54]
10. Dans le volet de droite, cliquez sur **Add Identity Provider**(Ajouter un fournisseur d’identité). 
   
    ![Configuration de l'authentification unique][55]

11. Dans la page **Identity Provider Settings** (Paramètres du fournisseur d’identité), effectuez les actions suivantes :
   
    ![Configuration de l'authentification unique][56]

    a. Dans la zone de texte **Nom** , entrez le nom de votre configuration. N’utilisez pas d’espaces.

    b. Collez l’**ID d’entité SAML** dans la zone de texte **Émetteur du fournisseur d’identité**.

    c. Collez l’**URL du service d’authentification unique SAML** dans la zone de texte **URL de connexion du fournisseur identité**.

    d. Collez l’**URL de déconnexion** dans la zone de texte **URL de déconnexion du fournisseur identité**.

    e. Sélectionnez **Sign AuthN Request**(Signer la demande d’authentification).

    f. Sous **Send AuthN request by** (Envoyer la demande d’authentification par), sélectionnez **POST**.

    g. Sous **Send logout request by** (Envoyer la demande de déconnexion par), sélectionnez **GET**.

12. Dans la section **Custom Attribute Mapping** (Mappage d’attributs personnalisé), choisissez le champ à mapper avec la revendication Azure AD. Dans cet exemple, la revendication **emailaddress** est mappée sur la valeur de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Il s’agit du nom de revendication par défaut d’Azure AD pour la revendication de courrier électronique. 
   
    > [!NOTE]
    > Utilisez l’**identificateur d’utilisateur** approprié pour mapper l’utilisateur d’Azure AD au mappage utilisateur DocuSign. Sélectionnez le champ correct et entrez la valeur appropriée en fonction des paramètres de votre organisation.
          
    ![Configuration de l'authentification unique][57]

13. Dans la section **Identity Provider Certificate** (Certificat du fournisseur d’identité), cliquez sur **Ajouter un certificat**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure AD.   
   
    ![Configuration de l'authentification unique][58]

14. Cliquez sur **Save**.

15. Dans la section **Identity Providers** (Fournisseurs d’identité), cliquez **Actions**, puis cliquez sur **Endpoints** (Points de terminaison).   
   
    ![Configuration de l'authentification unique][59]
 
16. Dans la section **View SAML 2.0 Endpoints** (Afficher les points de terminaison SAML 2.0) du **portail d’administration DocuSign**, effectuez les étapes suivantes :
   
    ![Configuration de l'authentification unique][60]
   
    a. Copiez la valeur **Service Provider Issuer URL** (URL de l’émetteur du fournisseur de service), puis collez-la dans la zone de texte **Identificateur** dans la section du portail Azure **DocuSign Domain and URLs** (Domaine et URL DocuSign), suivant le modèle : `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Copiez la valeur **Service Provider Login URL** (URL de connexion du fournisseur d’identité), puis collez-la dans la zone de texte **URL d’authentification** dans la section du portail Azure **DocuSign Domain and URLs** (Domaine et URL DocuSign), suivant le modèle : `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Configurer l’authentification unique](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Cliquez sur **Fermer**
    
17. Sur le portail Azure, cliquez sur **Enregistrer**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-docusign-test-user"></a>Création d’un utilisateur de test DocuSign

L’application prend en charge l’**approvisionnement juste-à-temps des utilisateurs** et, après authentification, les utilisateurs sont créés automatiquement dans l’application.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à DocuSign.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à DocuSign, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **DocuSign**.

    ![Configurer l’authentification unique](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette DocuSign dans le volet d’accès, vous devez être connecté automatiquement à votre application DocuSign.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement de l’utilisateur](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

