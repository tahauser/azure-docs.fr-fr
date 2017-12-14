---
title: "Didacticiel : Intégration d’Azure Active Directory à SumoLogic | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SumoLogic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 9b737ce50c1b52c771af275f5722bfd92b2f1491
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Didacticiel : Intégration d’Azure Active Directory à SumoLogic

Dans ce didacticiel, vous allez apprendre à intégrer SumoLogic avec Azure Active Directory (Azure AD).

L’intégration de SumoLogic dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SumoLogic
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SumoLogic (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à SumoLogic, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SumoLogic pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SumoLogic à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sumologic-from-the-gallery"></a>Ajout de SumoLogic à partir de la galerie
Pour configurer l’intégration de SumoLogic dans Azure AD, vous devez ajouter SumoLogic à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SumoLogic à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **SumoLogic**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_search.png)

5. Dans le panneau des résultats, sélectionnez **SumoLogic**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec SumoLogic, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SumoLogic équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SumoLogic associé doit être établie.

Dans SumoLogic, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SumoLogic, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SumoLogic](#creating-a-sumologic-test-user)** pour obtenir un équivalent de Britta Simon dans SumoLogic lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SumoLogic.

**Pour configurer l’authentification unique Azure AD avec SumoLogic, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SumoLogic**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_samlbase.png)

3. Dans la section **Domaine et URL SumoLogic**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.SumoLogic.com`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SumoLogic](https://www.sumologic.com/contact-us/). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de SumoLogic**, cliquez sur **Configurer SumoLogic** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise SumoLogic en tant qu’administrateur.

8. Accédez à **Manage \> Security**.
   
    ![Gestion](./media/active-directory-saas-sumologic-tutorial/ic778556.png "Gestion")

9. Cliquez sur **SAML**.
   
    ![Paramètres de sécurité globaux](./media/active-directory-saas-sumologic-tutorial/ic778557.png "Paramètres de sécurité globaux")

10. Dans la liste **Select a configuration or create a new one**, sélectionnez **Azure AD**, puis cliquez sur **Configure**.
   
    ![Configurer SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778558.png "Configurer SAML 2.0")

11. Dans la boîte de dialogue **Configure SAML 2.0** , procédez comme suit :
   
    ![Configurer SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778559.png "Configurer SAML 2.0")
   
    a. Dans la zone de texte **Configuration Name**, entrez **Azure AD**. 

    b. Sélectionnez **Debug Mode**.

    c. Dans la zone de texte **Issuer**, collez la valeur de **ID d’entité SAML** que vous avez copiée depuis le portail Azure. 

    d. Dans la zone de texte **Authn Request URL**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    e. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .

    f. Dans **Email Attribute**, sélectionnez **Use SAML subject**.  

    g. Sélectionnez **SP initiated Login Configuration**.

    h. Dans la zone de texte **Login Path** (Chemin d’accès de connexion), entrez **Azure**, puis cliquez sur **Save** (Enregistrer).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-sumologic-test-user"></a>Création d’un utilisateur de test SumoLogic

Pour se connecter à SumoLogic, les utilisateurs d’Azure AD doivent être approvisionnés dans SumoLogic.  

* Dans le cas de SumoLogic, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **SumoLogic** .

2. Accédez à **Gérer \> Utilisateurs**.
   
    ![Utilisateurs](./media/active-directory-saas-sumologic-tutorial/ic778561.png "Utilisateurs")

3. Cliquez sur **Add**.
   
    ![Utilisateurs](./media/active-directory-saas-sumologic-tutorial/ic778562.png "Utilisateurs")

4. Dans la boîte de dialogue **New User** , procédez comme suit :
   
    ![Nouvel utilisateur](./media/active-directory-saas-sumologic-tutorial/ic778563.png "Nouvel utilisateur") 
 
    a. Indiquez dans les zones de texte **First Name**, **Last Name** et **Email** le prénom, le nom et l’adresse e-mail du compte Azure AD valide que vous souhaitez approvisionner.
  
    b. Sélectionnez un rôle
  
    c. Pour **Status (Statut)**, sélectionnez **Active (Actif)**.
  
    d. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SumoLogic, pour approvisionner des comptes utilisateur AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SumoLogic.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SumoLogic, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SumoLogic**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette SumoLogic dans le panneau d’accès, vous devez être connecté automatiquement à votre application SumoLogic.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_203.png

