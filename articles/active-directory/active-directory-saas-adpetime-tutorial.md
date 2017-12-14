---
title: "Didacticiel : Intégration d’Azure Active Directory à ADP eTime | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et ADP eTime."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a3e9f0be-19ed-4b99-a180-619e7624fc26
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: a8ed643d0609eeb9a5a505aeac3dae5183a6c32e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Didacticiel : Intégration d’Azure AD à ADP eTime

Dans ce didacticiel, vous allez apprendre à intégrer ADP eTime à Azure Active Directory (Azure AD).

L’intégration d’ADP eTime dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ADP eTime.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ADP eTime (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à ADP eTime, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ADP eTime pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ADP eTime à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adp-etime-from-the-gallery"></a>Ajout de ADP eTime à partir de la galerie
Pour configurer l’intégration d’ADP eTime à Azure AD, vous devez ajouter ADP eTime à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ADP eTime à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **ADP eTime**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_search.png)

5. Dans le volet de résultats, sélectionnez **ADP eTime**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ADP eTime, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur ADP eTime correspondant à l’utilisateur Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur ADP eTime associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans ADP eTime.

Pour configurer et tester l’authentification unique Azure AD avec ADP eTime, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur test ADP eTime](#creating-an-adp-etime-test-user)** pour avoir un équivalent de Britta Simon dans ADP eTime lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ADP eTime.

**Pour configurer l’authentification unique Azure AD avec ADP eTime, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **ADP eTime**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_samlbase.png)

3. Dans la section **Domaine et URL ADP eTime**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<servername>.adp.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<servername>.adp.com/affwebservices/public/saml2assertionconsumer` 
 
    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support ADP eTime](https://www.adp.com/contact-us/overview.aspx).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_certificate.png) 

5. L’application ADP eTime s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre un exemple : Le nom de la revendication sera toujours **« PersonImmutableID »** et sa valeur mappée à ExtensionAttribute2 qui contient la valeur EmployeeID de l’utilisateur. 

    Ici, le mappage utilisateur d’Azure AD à ADP eTime est effectué avec EmployeeID, mais vous pouvez le mapper à une valeur différente également basée sur les paramètres de votre application. Par conséquent, contactez d’abord [l’équipe du support ADP eTime](https://www.adp.com/contact-us/overview.aspx) pour utiliser le bon identificateur d’utilisateur et mapper cette valeur à la revendication **« PersonImmutableID »**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.extensionattribute2 |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

    > [!NOTE] 
    > Avant de pouvoir configurer votre assertion SAML, vous devez contacter [l’équipe du support ADP eTime](https://www.adp.com/contact-us/overview.aspx) et lui demander d’affecter la valeur d’attribut d’identificateur unique à votre locataire. Vous avez besoin de cette valeur pour configurer la revendication personnalisée pour votre application. 

7. Dans la section **Configuration d’ADP eTime**, cliquez sur **Configurer ADP eTime** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_configure.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_general_400.png)

9. Pour configurer l’authentification unique du côté **ADP eTime**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe du support ADP eTime](https://www.adp.com/contact-us/overview.aspx). 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-adp-etime-test-user"></a>Création d’un utilisateur test ADP eTime

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ADP eTime. Pour ajouter les utilisateurs au compte ADP eTime, contactez [l’équipe du support ADP eTime](https://www.adp.com/contact-us/overview.aspx). 
   
### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ADP eTime.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à ADP eTime, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ADP eTime**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ADP eTime dans le volet d’accès, vous devez être connecté automatiquement à votre application ADP eTime.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png

