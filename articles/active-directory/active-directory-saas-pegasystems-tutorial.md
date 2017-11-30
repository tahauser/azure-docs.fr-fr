---
title: "Didacticiel : Intégration d’Azure Active Directory à Pega Systems | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pega Systems."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: ffc3d94416378f6469eaa574dc10880ef840e91f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Didacticiel : Intégration d’Azure Active Directory à Pega Systems

Dans ce didacticiel, vous allez apprendre à intégrer Pega Systems à Azure Active Directory (Azure AD).

L’intégration de Pega Systems dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Pega Systems.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Pega Systems (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Pega Systems, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Pega Systems pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Pega Systems à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-pega-systems-from-the-gallery"></a>Ajout de Pega Systems à partir de la galerie
Pour configurer l’intégration de Pega Systems à Azure AD, vous devez ajouter Pega Systems à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Pega Systems à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Pega Systems**, sélectionnez **Pega Systems** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Pega Systems dans la liste des résultats](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Pega Systems avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Pega Systems équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Pega Systems associé doit être établie.

Dans Pega Systems, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Pega Systems, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Pega Systems](#create-a-pega-systems-test-user)** pour avoir un équivalent de Britta Simon dans Pega Systems lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Pega Systems.

**Pour configurer l’authentification unique Azure AD avec Pega Systems, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Pega Systems**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Dans la section **Domaine et URL Pega Systems**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’état du relais exacts. Vous pouvez trouver les valeurs de l’identificateur et de l’URL de réponse dans l’application Pega (la procédure est expliquée plus loin dans ce didacticiel). Pour connaître la valeur de l’état du relais, contactez [l’équipe de support technique de Pega Systems](https://www.pega.com/contact-us). 

5. L’application Pega Systems attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. Ces revendications sont spécifiques au client et varient en fonction de vos besoins. Les revendications facultatives suivantes sont des exemples que vous pouvez configurer pour votre application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. 

    ![Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Téléphone | *********** |

    > [!NOTE]
    > Ces valeurs sont spécifiques au client. Indiquez les valeurs appropriées.

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Pour configurer l’authentification unique du côté de **Pega Systems**, ouvrez le **portail Pega** avec un compte administrateur dans une autre fenêtre de navigateur.

10. Sélectionnez **Créer** -> **SysAdmin** -> **Service d’authentification**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Effectuez les actions ci-après dans l’écran **Créer un service d’authentification** :

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Sélectionnez **SAML 2.0** dans Type

    b. Dans la zone de texte **Nom**, entrez un nom, par exemple Authentification unique Azure AD

    c. Dans la zone de texte **Brève description**, saisissez n’importe quelle description  

    d. Cliquez sur **Créer et ouvrir** 
    
12. Dans la section **Identity Provider (IdP) information** (Informations sur le fournisseur d’identité (IdP)), cliquez sur **Import IdP metadata** (Importer les métadonnées Idp) et explorez le fichier de métadonnées que vous avez téléchargé depuis le portail Azure. Cliquez sur **Envoyer** pour charger les métadonnées.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Vous renseignerez ainsi les données IdP comme illustré ci-dessous.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Effectuez les actions ci-après dans la section **Service Provider (SP) settings** (Paramètres du fournisseur de services) :

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copiez la valeur **Entity Identification** (Identification de l’entité) et collez-la dans la zone de texte **Identifier** (Identificateur) du Portail Azure.

    b.  Copiez la valeur **Assertion Consumer Service (ACS) location** (Emplacement de l’URL Assertion Consumer Service (ACS)) et collez-la dans la zone de texte **URL de réponse** du Portail Azure.

    c. Sélectionnez **Disable request signing** (Désactiver la signature de requête).

15. Cliquez sur **Enregistrer**.
    
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-pega-systems-test-user"></a>Créer un utilisateur test Pega Systems

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Pega Systems. Collaborez avec [l’équipe d’assistance technique Pega Systems](https://www.pega.com/contact-us) pour créer des utilisateurs dans Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pega Systems.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Pega Systems, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Pega Systems**.

    ![Lien Pega Systems dans la liste des applications](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Pega Systems dans le volet d’accès, vous devez être connecté automatiquement à votre application Pega Systems.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

