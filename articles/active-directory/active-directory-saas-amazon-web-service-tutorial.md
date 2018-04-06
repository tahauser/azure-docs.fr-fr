---
title: 'Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.openlocfilehash: 92189eba7df49aa45adaee7ee3c93c8972b5594b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS)

Dans ce tutoriel, vous allez apprendre à intégrer Amazon Web Services à Azure Active Directory (Azure AD).

L’intégration de Amazon Web Services (AWS) dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Services (AWS).
- Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Amazon Web Services (AWS) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD avec Amazon Web Services (AWS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Amazon Web Services (AWS) avec authentification unique

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Amazon Web Services (AWS) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Ajout d’Amazon Web Services (AWS) à partir de la galerie
Pour configurer l’intégration d’Amazon Web Services (AWS) avec Azure AD, vous devez ajouter Amazon Web Services (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Amazon Web Services (AWS) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Amazon Web Services (AWS)**, sélectionnez **Amazon Web Services (AWS)** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Amazon Web Services (AWS) dans la liste des résultats](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quelle est l’équivalence de l’utilisateur Amazon Web Services (AWS) dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Amazon Web Services (AWS) associé doit être établie.

Dans Amazon Web Services (AWS), affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)** pour avoir un équivalent de Britta Simon dans Amazon Web Services (AWS) lié à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Amazon Web Services (AWS).

**Pour configurer l’authentification unique Azure AD avec Amazon Web Services (AWS), procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Amazon Web Services (AWS)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Dans la section **Domaine et URL d’Amazon Web Services (AWS)**, l’utilisateur n’aura pas à effectuer les étapes que l’application a déjà intégrées préalablement avec Azure.

    ![Informations d’authentification unique dans Domaine et URL Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Votre application Amazon Web Services (AWS) attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configurer l’authentification unique attb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut  | Valeur de l’attribut | Espace de noms |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rôle            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Vous devez configurer l’approvisionnement des utilisateurs dans Azure AD pour extraire tous les rôles de la console AWS. Reportez-vous aux étapes de provisionnement ci-dessous.

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique add](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique addattb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.
    
    d. Cliquez sur **OK**.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Dans une autre fenêtre de navigateur, connectez-vous au site de votre entreprise Amazon Web Services (AWS) en tant qu’administrateur.

9. Cliquez sur **Console Home**.
   
    ![Démarrage dans la configuration de l’authentification unique][11]

10. Cliquez sur **Gestion de l’identité et de l’accès**. 
   
    ![Identité dans la configuration de l’authentification unique][12]

11. Cliquez sur **Identity Providers**, puis sur **Create Provider**. 
   
    ![Fournisseur dans la configuration de l’authentification unique][13]

12. Dans la page **Configure Provider** , procédez comme suit : 
   
    ![Boîte de dialogue de la configuration de l’authentification unique][14]
 
    a. Pour **Provider Type**, sélectionnez **SAML**.

    b. Dans la zone de texte **Provider Name** (Nom de fournisseur), tapez un nom de fournisseur (par exemple : *WAAD*).

    c. Pour charger le **fichier de métadonnées** téléchargé à partir du portail Azure, cliquez sur **Choose File** (Choisir un fichier).

    d. Cliquez sur **Next Step**.

13. Dans la page **Verify Provider Information**, cliquez sur **Create**. 
    
    ![Vérification dans la configuration de l’authentification unique][15]

14. Cliquez sur **Roles**, puis sur **Create New Role**. 
    
    ![Rôles dans la configuration de l’authentification unique][16]

15. Dans la boîte de dialogue **Set Role Name** , procédez comme suit : 
    
    ![Nom dans la configuration de l’authentification unique][17] 

    a. Dans la zone de texte **Role Name** (Nom de rôle), tapez un nom de rôle (par exemple : *Utilisateur_test*). 

    b. Cliquez sur **Next Step**.

16. Dans la boîte de dialogue **Select Role Type** , procédez comme suit : 
    
    ![Type de rôle dans la configuration de l’authentification unique][18] 

    a. Sélectionnez **Role For Identity Provider Access**. 

    b. Dans la section **Grant Web Single Sign-On (WebSSO) access to SAML providers**, cliquez sur **Select**.

17. Dans la boîte de dialogue **Establish Trust** , procédez comme suit :  
    
    ![Approbation dans la configuration de l’authentification unique][19] 

    a. Pour le fournisseur SAML, sélectionnez celui que vous avez déjà créé (par exemple : *WAAD*) 
  
    b. Cliquez sur **Next Step**.

18. Dans la boîte de dialogue **Verify Role Trust**, cliquez sur **Next Step**. 
    
    ![Approbation du rôle dans la configuration de l’authentification unique][32]

19. Dans la boîte de dialogue **Attach Policy**, cliquez sur **Next Step**.  
    
    ![Stratégie dans la configuration de l’authentification unique][33]

20. Dans la boîte de dialogue **Review** , procédez comme suit :   
    
    ![Révision dans la configuration de l’authentification unique][34] 

    a. Cliquez sur **Create Role**.

    b. Créez autant de rôles que nécessaire et mappez-les vers le fournisseur d’identité.

21. Utilisez les informations d’identification du compte de service AWS pour récupérer les rôles depuis le compte AWS dans Attribution d’utilisateurs d’Azure AD. Pour ce faire, ouvrez la page d’accueil de la console AWS.

22. Cliquez sur **Services** (Services) -> **Security,Identity& Compliance** (Sécurité, identité et conformité) -> **IAM**.

    ![Extraction des rôles à partir du compte AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Sélectionnez l’onglet **Policies** (Stratégies) dans la section IAM.

    ![Extraction des rôles à partir du compte AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Créez une stratégie en cliquant sur **Create policy** (Créer une stratégie).

    ![Création d’une stratégie](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Créez votre propre stratégie pour extraire tous les rôles à partir de comptes AWS en procédant comme suit :

    ![Création d’une stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. Dans la section **Créer une stratégie**, cliquez sur l’onglet **JSON**.

    b. Dans le document de stratégie, ajoutez l’extrait au format JSON ci-dessous.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam:ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Cliquez sur le bouton **Vérifier la stratégie** pour valider la stratégie.

    ![Définition de la nouvelle stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Définissez la **nouvelle stratégie** en suivant ces étapes :

    ![Définition de la nouvelle stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Indiquez **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD) comme **Policy Name** (Nom de stratégie).

    b. Vous pouvez fournir une **Description** (Description) pour la stratégie, par exemple **This policy will allow to fetch the roles from AWS accounts** (Cette stratégie permettra de récupérer les rôles à partir de comptes AWS).
    
    c. Cliquez sur le bouton **Créer une stratégie**.
        
27. Créez un compte d’utilisateur dans AWS IAM Service en suivant ces étapes :

    a. Cliquez sur **Users** (Utilisateurs) dans la console AWS IAM.

    ![Définition de la nouvelle stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Cliquez sur le bouton **Add user** (Ajouter un utilisateur) permettant de créer un utilisateur.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Dans la section **Add User** (Ajouter un utilisateur), procédez comme suit :
    
    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Entrez **AzureADRoleManager** (GestionnaireDeRôlesAzureAD) comme nom d’utilisateur.
    
    * Pour le type d’accès, sélectionnez l’option **Programmatic access** (Accès par programmation). Ainsi, l’utilisateur peut appeler les API et extraire les rôles du compte AWS.
    
    * Cliquez sur le bouton **Next Permissions** (Autorisations suivantes) dans le coin inférieur droit.

28. À présent, créez une stratégie pour cet utilisateur en effectuant les étapes suivantes :

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Cliquez sur le bouton **Attach existing policies directly** (Joindre les stratégies existantes directement).

    b. Recherchez la stratégie nouvellement créée dans la section du filtre **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD).
    
    c. Sélectionnez la **stratégie**, puis cliquez sur le bouton **Next: Review** (Suivant : Vérification).

29. Passez en revue la stratégie pour l’utilisateur joint en suivant ces étapes :

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Vérifiez le nom d’utilisateur, le type d’accès et la stratégie mappée à l’utilisateur.
    
    b. Cliquez sur le bouton **Create user** (Créer un utilisateur) dans le coin inférieur droit pour créer l’utilisateur.

30. Téléchargez les informations d’identification d’un utilisateur en procédant comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copiez les **Access key ID** (ID de la clé d’accès) et **Secret access key** (Clé d’accès secrète) de l’utilisateur.
    
    b. Dans la section d’attribution d’utilisateurs d’Azure AD, entrez ces informations d’identification pour extraire les rôles à partir de la console AWS.
    
    c. Cliquez sur le bouton **Close** (Fermer) au bas de l’écran.

31. Accédez à la section **User Provisioning** (Attribution d’utilisateurs) de l’application Amazon Web Services dans le portail de gestion Azure AD.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Entrez la **Clé d’accès** et le **Secret** dans les champs **Clé secrète client** et **Jeton secret** respectivement.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Entrez la clé d’accès utilisateur AWS dans le champ **clientsecret**.
    
    b. Entrez le secret d’utilisateur AWS dans le champ **Jeton secret**.
    
    c. Cliquez sur le bouton **Tester la connexion** pour vérifier que cette connexion est correctement établie.

    d. Enregistrez le paramètre en cliquant sur le bouton **Enregistrer** en haut.
 
33. À présent, assurez-vous de régler sur **Actif** l’état de mise en service dans la section Paramètres, en agissant sur le commutateur, puis en cliquant sur le bouton **Enregistrer** en haut.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Créer un utilisateur de test Amazon Web Services (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Services (AWS). Comme Amazon Web Services (AWS) ne nécessite pas de création d’utilisateur dans son système pour l’authentification unique, vous n’avez aucune action à accomplir ici.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autoriserz Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Amazon Web Services (AWS).

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à Amazon Web Services (AWS), procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Amazon Web Services (AWS)**.

    ![Lien Amazon Web Services (AWS) dans la liste des applications](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Amazon Web Services (AWS) dans le volet d’accès, vous devez être connecté automatiquement à votre application Amazon Web Services (AWS).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

