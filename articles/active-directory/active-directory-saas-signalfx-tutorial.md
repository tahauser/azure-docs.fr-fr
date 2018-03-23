---
title: 'Didacticiel : Intégration d’Azure Active Directory avec SignalFx | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: jeedes
ms.openlocfilehash: 50a86a01c22450ae2d92e6743fb6de7e652d4017
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Didacticiel : Intégration d’Azure Active Directory à SignalFx

L’objectif de ce didacticiel est de vous apprendre à intégrer SignalFx à Azure Active Directory (Azure AD).

L’intégration de SignalFx dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SignalFx.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SignalFx (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à SignalFx, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SignalFx pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SignalFx à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-signalfx-from-the-gallery"></a>Ajout de SignalFx à partir de la galerie
Pour configurer l’intégration de SignalFx à Azure AD, vous devez ajouter SignalFx à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SignalFx à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SignalFx**, sélectionnez **SignalFx** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SignalFx dans la liste des résultats](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SignalFx avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SignalFx équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SignalFx associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SignalFx, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test SignalFx](#create-a-signalfx-test-user)** pour avoir un équivalent de Britta Simon dans SignalFx lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SignalFx.

**Pour configurer l’authentification unique Azure AD avec SignalFx, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **SignalFx**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Dans la section **Domaine et URL SignalFx**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://api.signalfx.com/v1/saml/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > La valeur ci-dessus n’est pas une valeur réelle. Vous mettez à jour la valeur avec l’URL de réponse réelle. La procédure est expliquée plus loin dans le didacticiel.

4. L’application SignalFx attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :   

    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique Add](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique Addattb](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.
 
6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. Pour générer **l’URL des métadonnées**, effectuez les étapes suivantes :

    a. Cliquez sur **Inscriptions des applications**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_appregistrations.png)
   
    b. Cliquez sur **Points de terminaison** pour ouvrir la boîte de dialogue **Points de terminaison**.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_endpointicon.png)

    c. Cliquez sur le bouton Copier pour copier l’URL du document de métadonnées de fédération (**FEDERATION METADATA DOCUMENT**), puis collez-la dans le Bloc-notes.
    
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_endpoint.png)
     
    d. Accédez maintenant à la page de propriétés de **SignalFx**, puis copiez **l’ID d’application** à l’aide du bouton **Copier** et collez-le dans le Bloc-notes.
 
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_appid.png)

    e. Générez l’**URL des métadonnées** en utilisant le format suivant : `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

9. Dans la section **Configuration de SignalFx**, cliquez sur **Configurer SignalFx** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

10. Connectez-vous à votre site d’entreprise SignalFx en tant qu’administrateur.

11. Dans SignalFx, en haut de l’écran, cliquez sur **Intégrations** pour ouvrir la page correspondante.

    ![Intégration de SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

12. Cliquez sur la vignette **Azure Active Directory** sous la section **Services de connexion**.
 
    ![SignalFx saml](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

13. Cliquez sur **NOUVELLE INTÉGRATION** puis, sous l’onglet **INSTALLER**, procédez comme suit :
 
    ![SignalFx samlintgpage](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Dans la zone de texte **Nom**, saisissez un nouveau nom d’intégration, par exemple **NomDeNotreOrg SAML SSO**.

    b. Copiez la valeur de **l’ID d’intégration** et ajoutez comme suffixe **l’URL de réponse** comme `https://api.signalfx.com/v1/saml/acs/<integration ID>` dans la zone de texte **URL de réponse** de la section **Domaines et URL SignalFx** du portail Azure.

    c. Cliquez sur **Charger le fichier** pour charger le **certificat encodé en Base64** qui a été téléchargé à partir du portail Azure dans la zone de texte **Certificat**.

    d. Dans la zone de texte **URL de l’émetteur**, collez la valeur **ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **URL des métadonnées**, collez la valeur **URL des métadonnées** générée à partir du portail Azure.

    f. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-signalfx-test-user"></a>Créer un utilisateur de test SignalFx

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SignalFx. SignalFx prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à SignalFx s’il n’existe pas déjà.

Lorsqu’un utilisateur se connecte pour la première fois à SignalFx à partir de la SSO SAML, [l’équipe de support SignalFx](mailto:kmazzola@signalfx.com) lui envoie un e-mail contenant un lien sur lequel il doit cliquer pour s’authentifier. Cette procédure n’intervient que lors de la première connexion de l’utilisateur ; les tentatives de connexion suivantes ne nécessitent aucune validation par e-mail.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support SignalFx](mailto:kmazzola@signalfx.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SignalFx.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SignalFx, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SignalFx**.

    ![Lien SignalFx dans la liste des applications](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque SignalFx dans le volet d’accès, vous devez être connecté automatiquement à votre application SignalFx.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png

