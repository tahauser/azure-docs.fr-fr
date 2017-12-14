---
title: "Didacticiel : Intégration d’Azure Active Directory à Marketo | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Marketo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 10ce59668127ff918a85caf537e8ab495c542278
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Didacticiel : Intégration d’Azure Active Directory à Marketo

Dans ce didacticiel, vous allez apprendre à intégrer Marketo à Azure Active Directory (Azure AD).

L’intégration de Marketo à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès Marketo.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Marketo (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Marketo, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Marketo pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Marketo depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-marketo-from-the-gallery"></a>Ajout de Marketo depuis la galerie
Pour configurer l’intégration de Marketo à Azure AD, vous devez ajouter Marketo, depuis la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Marketo depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Marketo**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. Dans le panneau des résultats, sélectionnez **Marketo**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Marketo grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Marketo équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Marketo associé.

Dans Marketo, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Marketo, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Marketo](#creating-a-marketo-test-user)** pour avoir un équivalent de Britta Simon dans Marketo qui est lié à la représentation d’un utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Marketo.

**Pour configurer l’authentification unique Azure AD avec Marketo, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Marketo**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. Dans la section **Marketo Domain and URLs** (Domaine et URL Marketo), réalisez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://saml.marketo.com/sp`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support Marketo](http://investors.marketo.com/contactus.cfm).
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration de Marketo**, cliquez sur **Configurer Marketo**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Pour obtenir l’ID Munchkin de votre application, connectez-vous à Marketo à l’aide des informations d’identification d’administrateur et procédez comme suit :
   
    a. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.
   
    b. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Accédez au menu Intégration et cliquez sur le **lien Munchkin**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copiez l’ID Munchkin indiqué sur l’écran et complétez votre URL de réponse dans l’Assistant de configuration Azure AD.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Pour configurer l’authentification unique de l’application, suivez les étapes ci-dessous :
   
    a. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.
   
    b. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Accédez au menu Intégration et cliquez sur **Authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Pour activer les paramètres SAML, cliquez sur le bouton **Modifier**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Activez** les paramètres d’authentification unique.
   
    f. Collez l’**ID d’entité SAML** dans la zone de texte **ID de l’émetteur**.
   
    g. Dans la zone de texte **ID d’entité**, tapez l’URL `http://saml.marketo.com/sp`.
   
    h. Sélectionnez l’emplacement d’ID utilisateur en tant **qu’élément Identificateur de nom**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Si votre identificateur d’utilisateur n’est pas une valeur UPN, alors changez la valeur dans l’onglet Attribut.
   
    i. Chargez le certificat que vous avez téléchargé à partir de l’Assistant de configuration Azure AD. **Enregistrez** les paramètres.
   
    j. Modifiez les paramètres des pages de redirection.
   
    k. Collez l’**URL du service d’authentification unique SAML** dans la zone de texte **URL de connexion**.
   
    l. Collez l’**URL de déconnexion** dans la zone de texte **URL de déconnexion**.
   
    m. Dans l’**URL d’erreur**, copiez l’**URL de votre instance Marketo**, puis cliquez sur le bouton **Enregistrer** pour enregistrer les paramètres.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Pour activer l’authentification unique pour les utilisateurs, procédez comme suit :
   
    a. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.
   
    b. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Accédez au menu **Sécurité** et cliquez sur **Paramètres de connexion**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Vérifiez l’option **Exiger l’authentification unique** et **enregistrez** les paramètres.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-marketo-test-user"></a>Créer un utilisateur de test Marketo

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Marketo. Pour créer un utilisateur dans la plateforme Marketo, suivez ces étapes.

1. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.

2. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Accédez au menu **Sécurité** et cliquez sur **Utilisateurs et rôles**
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Dans l’onglet Utilisateurs, cliquez sur le lien **Inviter un nouvel utilisateur**
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. Dans l’Assistant d’invitation d’un nouvel utilisateur, renseignez les informations suivantes.
   
    a. Entrez l’adresse **e-mail** de l’utilisateur dans la zone de texte.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Entrez le **prénom** dans la zone de texte.
   
    c. Entrez le **nom** dans la zone de texte
   
    d. Cliquez sur **Suivant**

6. Dans l’onglet **Autorisations**, sélectionnez les **rôles d’utilisateur** et cliquez sur **Suivant**
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Pour envoyer l’invitation d’utilisateur, cliquez sur le bouton **Envoyer**
   
    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. L’utilisateur recevra une notification par e-mail. Pour activer le compte, il devra cliquer sur le lien et modifier le mot de passe. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Marketo.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Marketo, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Marketo**.

    ![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Marketo dans le volet d’accès, vous êtes connecté automatiquement à votre application Marketo.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

