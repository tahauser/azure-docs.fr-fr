---
title: "Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS) | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Web Services (AWS)."
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
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS)

Dans ce tutoriel, vous allez apprendre à intégrer Amazon Web Services à Azure Active Directory (Azure AD).

L’intégration de Amazon Web Services (AWS) dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Services (AWS).
- Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Amazon Web Services (AWS) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Amazon Web Services (AWS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Amazon Web Services (AWS) avec authentification unique

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Amazon Web Services (AWS) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Ajouter Amazon Web Services (AWS) à partir de la galerie
Pour configurer l’intégration d’Amazon Web Services (AWS) avec Azure AD, vous devez ajouter Amazon Web Services (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Amazon Web Services (AWS) à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)**, sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Amazon Web Services (AWS)**. Sélectionnez **Amazon Web Services (AWS)** dans le panneau des résultats, puis sélectionnez le bouton **Ajouter** pour ajouter l’application.

    ![Amazon Web Services (AWS) dans la liste des résultats](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Amazon Web Services (AWS) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Amazon Web Services (AWS) équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et un utilisateur Amazon Web Services (AWS) associé.

Pour établir le lien dans Amazon Web Services (AWS), la valeur **Nom d’utilisateur** doit être la même que celle du **nom d’utilisateur** Azure AD. 

Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), suivez les étapes de base suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user) pour avoir un équivalent de Britta Simon dans Amazon Web Services (AWS) lié à sa représentation dans Azure AD.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Amazon Web Services (AWS).

**Pour configurer l’authentification unique Azure AD avec Amazon Web Services (AWS), effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Amazon Web Services (AWS)**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, dans la zone de liste déroulante **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Dans la section **Domaine et URL d’Amazon Web Services (AWS)**, l’utilisateur n’a aucune action à effectuer, car l’application est déjà préintégrée à Azure.

    ![Informations du domaine et des URL Amazon Web Services (AWS) pour l’authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. L’application Amazon Web Services (AWS) attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante en donne un exemple :

    ![Configurer l’attribut d’authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. Dans la section **Attributs utilisateur** de la zone **Authentification unique**, configurez l'attribut de jeton SAML comme illustré dans l'image précédente, puis effectuez les étapes suivantes :
    
    | Nom de l’attribut  | Valeur de l’attribut | Espace de noms |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rôle            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Configurez le provisionnement des utilisateurs dans Azure AD pour récupérer tous les rôles de la console Amazon Web services (AWS). Reportez-vous aux étapes de provisionnement suivantes.

    a. Pour ouvrir la boîte de dialogue **Ajouter un attribut**, cliquez sur **Ajouter un attribut**.

    ![Configurer l’attribut d’authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configurer l’attribut d’authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur**, tapez la valeur de l’attribut correspondant à cette ligne.

    d. Dans la zone **Espace de noms**, tapez la valeur d’espace de noms correspondant à cette ligne.
    
    d. Sélectionnez **OK**.

6. Dans la section **Certificat de signature SAML**, sélectionnez **XML des métadonnées**. Ensuite, enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Amazon Web Services (AWS) comme administrateur.

9. Sélectionnez **Page d’accueil de la console**.
   
    ![Configurer la page d’accueil pour l’authentification unique][11]

10. Sélectionnez **Gestion des identités et des accès**. 
   
    ![Configurer une identité d’authentification unique][12]

11. Sélectionnez **Fournisseurs d’identité**. Sélectionnez ensuite **Créer un fournisseur**. 
   
    ![Configurer un fournisseur d’authentification unique][13]

12. Dans la boîte de dialogue **Configurer un fournisseur**, effectuez les étapes suivantes : 
   
    ![Boîte de dialogue Configurer l’authentification unique][14]
 
    a. Pour **Type de fournisseur**, sélectionnez **SAML**.

    b. Dans la zone **Nom de fournisseur**, tapez un nom de fournisseur (par exemple : *WAAD*).

    c. Pour charger le **fichier de métadonnées** téléchargé à partir du portail Azure, sélectionnez **Choisir un fichier**.

    d. Sélectionnez **Étape suivante**.

13. Dans la boîte de dialogue **Vérifier les informations du fournisseur**, sélectionnez **Créer**. 
    
    ![Configurer la vérification pour l'authentification unique][15]

14. Sélectionnez **Rôles**. Sélectionnez ensuite **Créer un rôle**. 
    
    ![Configurer les rôles d’authentification unique][16]

15. Dans la boîte de dialogue **Définir un nom de rôle**, effectuez les étapes suivantes : 
    
    ![Configurer un nom pour l’authentification unique][17] 

    a. Dans la zone **Nom de rôle**, tapez un nom de rôle (par exemple : *Utilisateur_test*). 

    b. Sélectionnez **Étape suivante**.

16. Dans la boîte de dialogue **Sélectionner un type de rôle**, effectuez les étapes suivantes : 
    
    ![Configurer un type de rôle pour l’authentification unique][18] 

    a. Sélectionnez **Role For Identity Provider Access**. 

    b. Dans la section **Grant Web Single Sign-On (WebSSO) access to SAML providers**, cliquez sur **Select**.

17. Dans la boîte de dialogue **Établir l’approbation**, effectuez les étapes suivantes :  
    
    ![Configurer l’approbation pour l’authentification unique][19] 

    a. Sélectionnez le fournisseur SAML que vous avez déjà créé (par exemple : *WAAD*). 
  
    b. Sélectionnez **Étape suivante**.

18. Dans la boîte de dialogue **Vérifier l’approbation du rôle**, sélectionnez **Étape suivante**. 
    
    ![Approbation du rôle dans la configuration de l’authentification unique][32]

19. Dans la boîte de dialogue **Attacher une stratégie**, sélectionnez **Étape suivante**.  
    
    ![Stratégie dans la configuration de l’authentification unique][33]

20. Dans la boîte de dialogue **Vérification**, effectuez les étapes suivantes :   
    
    ![Configurer la vérification pour l’authentification unique][34] 

    a. Sélectionnez **Créer un rôle**.

    b. Créez autant de rôles que nécessaire et mappez-les au fournisseur d’identité.

21. Utilisez les informations d’identification du compte de service Amazon Web Services (AWS) pour récupérer les rôles à partir du compte Amazon Web Services (AWS) dans le provisionnement d’utilisateurs Azure AD. Pour démarrer cette tâche, ouvrez la page d’accueil de la console Amazon Web Services (AWS).

22. Sélectionnez **Services** > **Sécurité, identité et conformité** > **IAM**.

    ![Récupération des rôles du compte Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Dans la section IAM, sélectionnez l’onglet **Stratégies**.

    ![Récupération des rôles du compte Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Pour créer une stratégie, sélectionnez **Créer une stratégie**.

    ![Création d’une stratégie](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Pour créer votre propre stratégie afin de récupérer tous les rôles des comptes Amazon Web Services (AWS), effectuez les étapes suivantes :

    ![Création d’une stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. Dans la section **Créer une stratégie**, sélectionnez l’onglet **JSON**.

    b. Dans le document de stratégie, ajoutez le code JSON suivant :
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Pour valider la stratégie, sélectionnez le bouton **Vérifier la stratégie**.

    ![Définition de la nouvelle stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Définissez la **nouvelle stratégie** en suivant ces étapes :

    ![Définition de la nouvelle stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Indiquez **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD) comme **Policy Name** (Nom de stratégie).

    b. Vous pouvez fournir la **Description**suivante pour la stratégie : **Cette stratégie permet de récupérer les rôles des comptes AWS**.
    
    c. Sélectionnez le bouton **Créer une stratégie**.
        
27. Pour créer un compte d’utilisateur dans le service IAM d’Amazon Web Services (AWS), effectuez les étapes suivantes :

    a. Sélectionnez **Utilisateurs** dans la console IAM d’Amazon Web Services (AWS).

    ![Définition de la nouvelle stratégie](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Pour créer un utilisateur, sélectionnez le bouton **Ajouter un utilisateur**.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Dans la section **Ajouter un utilisateur**, effectuez les étapes suivantes :
    
    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Entrez **AzureADRoleManager** dans la zone de nom d’utilisateur.
    
    * Pour le type d’accès, sélectionnez l’option **Accès par programmation**. De cette façon, l’utilisateur peut appeler les API et récupérer les rôles du compte Amazon Web Services (AWS).
    
    * Sélectionnez le bouton **Suivant : Autorisations** en bas à droite.

28. Créez une stratégie pour cet utilisateur en effectuant les étapes suivantes :

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Sélectionnez le bouton **Attacher directement les stratégies existantes**.

    b. Recherchez la stratégie nouvellement créée dans la section du filtre **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD).
    
    c. Sélectionnez la **stratégie**. Sélectionnez ensuite le bouton **Suivant : Vérification**.

29. Vérifiez la stratégie pour l’utilisateur attaché en suivant ces étapes :

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Vérifiez le nom d’utilisateur, le type d’accès et la stratégie qui sont mappés à l’utilisateur.
    
    b. Pour créer l’utilisateur, sélectionnez le bouton **Créer un utilisateur** en bas à droite.

30. Téléchargez les informations d’identification d’un utilisateur en effectuant les étapes suivantes :

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copiez les **Access key ID** (ID de la clé d’accès) et **Secret access key** (Clé d’accès secrète) de l’utilisateur.
    
    b. Entrez ces informations d’identification dans la section de provisionnement des utilisateurs Azure AD pour récupérer les rôles à partir de la console Amazon Web Services (AWS).
    
    c. Sélectionnez le bouton **Fermer** en bas à droite.

31. Accédez à la section **Attribution d’utilisateurs** de l’application Amazon Web Services (AWS) dans le portail de gestion Azure AD.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Entrez la **Clé d’accès** et le **Secret** dans les champs **Clé secrète client** et **Jeton secret** respectivement.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Entrez la clé d’accès utilisateur Amazon Web Services (AWS) dans le champ **clientsecret**.
    
    b. Entrez le secret utilisateur Amazon Web Services (AWS) dans le champ **Jeton secret**.
    
    c. Sélectionnez le bouton **Tester la connexion**. Vous devez pouvoir tester cette connexion.

    d. Enregistrez le paramètre en sélectionnant le bouton **Enregistrer** en haut.
 
33. Vérifiez que vous avez **activé** l’état de provisionnement dans **Paramètres**. Pour cela, sélectionnez **Activé**, puis le bouton **Enregistrer** en haut.

    ![Ajouter un utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**. Ensuite, accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée en consultant la [documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**. Puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Créer un utilisateur de test Amazon Web Services (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Services (AWS). Comme vous n’avez pas besoin de créer un utilisateur dans le système Amazon Web Services (AWS) pour l’authentification unique, vous n’avez aucune action à effectuer ici.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Amazon Web Services (AWS).

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Amazon Web Services (AWS), effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications. Accédez ensuite à la vue de répertoire et sélectionnez **Applications d’entreprise**. Ensuite, sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Amazon Web Services (AWS)**.

    ![Lien Amazon Web Services (AWS) dans la liste des applications](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**. 

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette Amazon Web Services (AWS) dans le volet d’accès, vous devez être connecté automatiquement à votre application Amazon Web Services (AWS). Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md). 

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

