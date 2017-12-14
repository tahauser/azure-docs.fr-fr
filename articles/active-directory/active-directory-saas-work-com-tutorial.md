---
title: "Didacticiel : Intégration d’Azure Active Directory à Work.com | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Work.com."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7aa04faab5da9ee7dae977be3a8c040c3aed0b9c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Didacticiel : Intégration d’Azure AD à Work.com

Dans ce didacticiel, vous allez apprendre à intégrer Work.com dans Azure Active Directory (Azure AD).

L’intégration de Work.com dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Work.com.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Work.com (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Work.com, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Work.com pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Work.com à partir de la galerie
2. Configurer et tester l’authentification unique Azure AD

## <a name="add-workcom-from-the-gallery"></a>Ajouter Work.com à partir de la galerie
Pour configurer l’intégration de Work.com à Azure AD, vous devez ajouter Work.com à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Work.com à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Work.com**, sélectionnez **Work.com** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Ajouter à partir de la galerie](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Work.com avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Work.com correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Work.com associé doit être établie.

Dans Work.com, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Work.com, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Work.com](#create-a-workcom-test-user)** pour avoir un équivalent de Britta Simon dans Work.com lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Work.com.

>[!NOTE]
>Pour configurer l’authentification unique, vous devez encore configurer un nom de domaine personnalisé Work.com. Vous devez définir au moins un nom de domaine, le tester, puis le déployer dans l’ensemble de votre entreprise.

**Pour configurer l’authentification unique Azure AD avec Work.com, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Work.com**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Authentification basée sur SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. Dans la section **Domaine et URL Work.com**, procédez comme suit :

    ![Section Domaine et URL Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique Work.com](https://help.salesforce.com/articleView?id=000159855&type=3). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Section Certificat de signature SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Work.com**, cliquez sur **Configurer Work.com** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Section Configuration de Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Connectez-vous à votre locataire Work.com en tant qu’administrateur.

8. Accédez à **Setup**.
   
    ![Configuration](./media/active-directory-saas-work-com-tutorial/ic794108.png "Configuration")

9. Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**. 
   
    ![Mon domaine](./media/active-directory-saas-work-com-tutorial/ic767825.png "mon domaine")

10. Pour vérifier que votre domaine a été configuré correctement, assurez-vous qu’il figure dans **Step 4 Deployed to Users**, puis passez en revue **My Domain Settings**.
   
    ![Domaine déployé sur l’utilisateur](./media/active-directory-saas-work-com-tutorial/ic784377.png "domaine déployé sur l’utilisateur")

11. Connectez-vous à votre locataire Work.com.

12. Accédez à **Setup**.
    
    ![Configuration](./media/active-directory-saas-work-com-tutorial/ic794108.png "Configuration")

13. Développez le menu **Security Controls**, puis cliquez sur **Single Sign-On Settings**.
    
    ![Paramètres d’authentification unique](./media/active-directory-saas-work-com-tutorial/ic794113.png "paramètres d’authentification unique")

14. Dans la page **Single Sign on Settings** , procédez comme suit :
    
    ![SAML activé](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML activé")
    
    a. Sélectionnez **SAML Enabled**.
    
    b. Cliquez sur **Nouveau**.

15. Dans la section **SAML Single Sign-On Settings** , procédez comme suit :
    
    ![Configuration de l’authentification unique SAML](./media/active-directory-saas-work-com-tutorial/ic794114.png "Configuration de l’authentification unique SAML")
    
    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.  
       
    > [!NOTE]
    > Le fait d’entrer une valeur pour **Name** renseigne automatiquement la zone de texte **API Name**.
    
    b. Dans la zone de texte **Émetteur**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis le portail Azure.
    
    c. Pour charger le certificat téléchargé à partir du portail Azure, cliquez sur **Parcourir**.
    
    d. Dans la zone de texte **ID d’entité**, tapez `https://salesforce-work.com`.
    
    e. Pour **SAML Identity Type (Type d’identité SAML)**, sélectionnez **Assertion contains the Federation ID from the User object (L’assertion contient l’ID de fédération de l’objet utilisateur)**.
    
    f. Pour **SAML Identity Location**, sélectionnez **Identity is in the NameIdentfier element of the Subject statement**.
    
    g. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    h. Dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
    
    i. Pour **Service Provider Initiated Request Binding**, sélectionnez **HTTP POST**.
    
    j. Cliquez sur **Enregistrer**.

16. Dans le volet de navigation gauche du portail classique Work.com, cliquez sur **Domain Management** pour développer la section associée, puis sur **My Domain** pour ouvrir la page **My Domain**. 
    
    ![Mon domaine](./media/active-directory-saas-work-com-tutorial/ic794115.png "mon domaine")

17. Dans la section **Login Page Branding** de la page **My Domain**, cliquez sur **Edit**.
    
    ![Personnalisation de la page de connexion](./media/active-directory-saas-work-com-tutorial/ic767826.png "personnalisation de la page de connexion")

14. Le nom des **SAML SSO Settings** s’affiche dans la section **Authentication Service** de la page **Login Page Branding**. Sélectionnez-le, puis cliquez sur **Save**.
    
    ![Personnalisation de la page de connexion](./media/active-directory-saas-work-com-tutorial/ic784366.png "personnalisation de la page de connexion")

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Utilisateurs et groupes -> Tous les utilisateurs](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Ajouter](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue utilisateur](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-workcom-test-user"></a>Créer un utilisateur de test Work.com
Pour que les utilisateurs d’Azure AD puissent se connecter, leur accès doit être approvisionné dans Work.com. Dans le cas de Work.com, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise Work.com en tant qu’administrateur.

2. Accédez à **Setup**.
   
    ![Configuration](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuration")
3. Accédez à **Manage Users \> Users**.
   
    ![Gestion des utilisateurs](./media/active-directory-saas-work-com-tutorial/IC784369.png "Gestion des utilisateurs")

4. Cliquez sur **New User**.
   
    ![Tous les utilisateurs](./media/active-directory-saas-work-com-tutorial/IC794117.png "Tous les utilisateurs")

5. Dans la section Modification de l’utilisateur, procédez comme suit pour les attributs d’un compte Azure AD valide que vous souhaitez configurer dans les zones de texte correspondantes :
   
    ![Modification de l’utilisateur](./media/active-directory-saas-work-com-tutorial/ic794118.png "modification de l’utilisateur")
   
    a. Dans la zone de texte **Prénom**, entrez le **prénom** de l’utilisateur **Britta**.
    
    b. Dans la zone de texte **Nom**, entrez le **nom** de l’utilisateur **Simon**.
    
    c. Dans la zone de texte **Alias**, entrez l’**alias** de l’utilisateur **BrittaS**.
    
    d. Dans la zone de texte **E-mail**, tapez l’**adresse e-mail** de l’utilisateur, **Brittasimon@contoso.com**.
    
    e. Dans la zone de texte **Nom d’utilisateur**, entrez un nom d’utilisateur, par exemple **Brittasimon@contoso.com**.
    
    f. Dans la zone de texte **Surnom**, entrez un **surnom**, par exemple **Simon**.
    
    g. Sélectionnez **Rôle**, **Licence utilisateur** et **Profil**.
    
    h. Cliquez sur **Enregistrer**.  
      
    > [!NOTE]
    > Le titulaire du compte Azure AD reçoit alors un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Work.com.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Work.com, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Work.com**.

    ![Work.com dans la liste des applications](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Work.com dans le volet d’accès, vous devez être connecté automatiquement à votre application Work.com.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

