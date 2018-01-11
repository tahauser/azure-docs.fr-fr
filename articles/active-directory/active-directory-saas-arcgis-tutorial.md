---
title: "Didacticiel : Intégration d’Azure Active Directory à ArcGIS Online | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et ArcGIS Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: b09dd977cbf5c4273667167217e86bb79ac2a9d8
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Didacticiel : Intégration d’Azure Active Directory à ArcGIS Online

Dans ce didacticiel, vous allez apprendre à intégrer ArcGIS Online à Azure Active Directory (Azure AD).

L’intégration d’ArcGIS Online à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous contrôlez qui a accès à ArcGIS Online.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ArcGIS Online (authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à ArcGIS Online, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ArcGIS Online pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’ArcGIS Online à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-arcgis-online-from-the-gallery"></a>Ajout d’ArcGIS Online à partir de la galerie
Pour configurer l’intégration d’ArcGIS Online à Azure AD, vous devez ajouter ArcGIS Online à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ArcGIS Online à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **ArcGIS Online**, sélectionnez **ArcGIS Online** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![ArcGIS Online dans la liste des résultats](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ArcGIS Online sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur ArcGIS Online correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur ArcGIS Online associé.

Dans ArcGIS Online, affectez la valeur du **nom d’utilisateur** indiquée dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ArcGIS Online, vous devez suivre les instructions des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test ArcGIS Online](#create-a-arcgis-online-test-user)** pour avoir dans ArcGIS Online un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application ArcGIS Online.

**Pour configurer l’authentification unique Azure AD avec ArcGIS Online, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **ArcGIS Online**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. Dans la section **Domaine et URL ArcGIS Online**, procédez comme suit :

    ![Informations d’authentification unique : domaine et URL ArcGIS Online](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.maps.arcgis.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support ArcGIS Online](http://support.esri.com/en/). 
 


4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ArcGIS en tant qu’administrateur.

7. Cliquez sur **Edit Settings** (Modifier les paramètres).

    ![Modifier les paramètres](./media/active-directory-saas-arcgis-tutorial/ic784742.png "Modifier les paramètres")

8. Cliquez sur **Sécurité**.

    ![Sécurité](./media/active-directory-saas-arcgis-tutorial/ic784743.png "Sécurité")

9. Sous **Enterprise Logins** (Informations de connexion entreprise), cliquez sur **Set Identity Provider** (Définir un fournisseur d’identité).

    ![Connexions de l’entreprise](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Connexions de l’entreprise")

10. Dans la page de configuration **Set Identity Provider** , procédez comme suit :
   
    ![Définir le fournisseur d’identité](./media/active-directory-saas-arcgis-tutorial/ic784745.png "Définir le fournisseur d’identité")
   
    a. Dans la zone de texte **Name** (Nom), tapez le nom de votre organisation.

    b. Pour **Metadata for the Enterprise Identity Provider will be supplied using**, sélectionnez **A File**.

    c. Pour télécharger votre fichier de métadonnées, cliquez sur **Choose file**.

    d. Cliquez sur **Set Identity Provider** (Définir un fournisseur d’identité).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-arcgis-online-test-user"></a>Créer un utilisateur de test ArcGIS Online

Pour permettre aux utilisateurs Azure AD de se connecter à ArcGIS Online, vous devez les attribuer dans ArcGIS Online.  
Pour ArcGIS Online, cette attribution s’effectue manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **ArcGIS** .

2. Cliquez sur **Invite Members** (Inviter des membres).
   
    ![Inviter des membres](./media/active-directory-saas-arcgis-tutorial/ic784747.png "Inviter des membres")

3. Sélectionnez **Add members automatically without sending an email** (Ajouter des membres automatiquement sans envoyer d’e-mail), puis cliquez sur **Next** (Suivant).
   
    ![Ajouter des membres automatiquement](./media/active-directory-saas-arcgis-tutorial/ic784748.png "Ajouter des membres automatiquement")

4. Dans la page de boîte de dialogue **Members** , procédez comme suit :
   
     ![Ajouter et vérifier](./media/active-directory-saas-arcgis-tutorial/ic784749.png "Ajouter et vérifier")
    
     a. Entrez **l’adresse e-mail**, le **prénom** et le **nom** d’un compte Azure AD valide que vous voulez attribuer.
  
     b. Cliquez sur **Add And Review** (Ajouter et vérifier).
5. Passez en revue les données que vous avez entrées, puis cliquez sur **Add Members** (Ajouter des membres).
   
    ![Ajouter un membre](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Ajouter un membre")
        
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à ArcGIS Online.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à ArcGIS Online, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ArcGIS Online**.

    ![Lien ArcGIS Online dans la liste des applications](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ArcGIS Online dans le volet d’accès, vous devez être authentifié automatiquement auprès de votre application ArcGIS Online.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

