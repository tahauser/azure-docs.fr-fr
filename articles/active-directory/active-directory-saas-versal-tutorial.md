---
title: "Didacticiel : Intégration d’Azure Active Directory à Versal | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Versal."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: 34ac0f6f98ba84584d468b5f199d8d6a713ede8a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Didacticiel : Intégration d’Azure Active Directory à Versal

Dans ce didacticiel, vous allez apprendre à intégrer Versal à Azure Active Directory (Azure AD).

L’intégration de Versal à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Versal.
- Vous pouvez permettre à vos utilisateurs d’être automatiquement authentifié sur Versal (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Versal, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Versal pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Versal à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-versal-from-the-gallery"></a>Ajouter Versal à partir de la galerie
Pour configurer l’intégration de Versal à Azure AD, vous devez ajouter Versal à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Versal à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Versal**, sélectionnez **Versal** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Versal dans la liste des résultats](./media/active-directory-saas-versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Versal, pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Versal équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Versal associé doit être établie.

Dans Versal, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Versal, vous devez suivre les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Versal](#create-a-versal-test-user)** pour avoir un équivalent de Britta Simon dans Versal lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application Versal.

**Pour configurer l’authentification unique Azure AD avec Versal, suivez les étapes ci-dessous :**

1. Sur la page d’intégration de l’application **Versal** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-versal-tutorial/tutorial_versal_samlbase.png)

3. Dans la section **Domaine et URL Versal**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique Domaine et URL Versal](./media/active-directory-saas-versal-tutorial/tutorial_versal_url.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur : `VERSAL`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE] 
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Contactez [l’équipe de support Versal](https://support.versal.com/hc/) pour obtenir cette valeur.
    
4. Votre application s’attend à recevoir les assertions SAML dans un certain format, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de votre jeton SAML. La capture d’écran suivante montre un exemple : La valeur par défaut **d’Identificateur d’utilisateur** est **user.userprincipalname**, mais **Versal** s’attend à ce qu’elle soit mappée avec l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.
    
    ![Menu déroulant Identificateur d’utilisateur](./media/active-directory-saas-versal-tutorial/tutorial_versal_attribute.png)

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-versal-tutorial/tutorial_versal_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-versal-tutorial/tutorial_general_400.png)
    
7. Pour configurer l’authentification unique côté **Versal**, vous devez envoyer le **XML de métadonnées** téléchargé et le **Certificat de signature SAML** à [l’équipe de support Versal](https://support.versal.com/hc/). Elle configurera votre organisation Versal afin que la connexion SSO SAML soit correctement mise en place des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-versal-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-versal-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-versal-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-versal-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-versal-test-user"></a>Créer un utilisateur de test Versal

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Versal. Veuillez suivre le guide de support [Créer un utilisateur de test SAML](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) pour créer l’utilisateur Britta Simon au sein de votre organisation. Les utilisateurs doivent être créés et activés dans Versal pour que vous puissiez utiliser l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Versal.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Versal, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Versal**.

    ![Lien Versal dans la liste des applications](./media/active-directory-saas-versal-tutorial/tutorial_versal_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide d’un cours Versal incorporé à votre site web.
Consultez le guide de support [Incorporer des cours professionnels](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **Authentification unique SAML** pour obtenir des instructions permettant d’incorporer un cours Versal avec prise en charge de l’authentification unique Azure AD. 

Vous devrez créer un cours, le partager avec votre organisation et le publier afin de tester son incorporation. Consultez [Créer un cours](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [Publier un cours](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course) et [Gestion des cours et des apprenants](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management) pour plus d’informations.  
                     

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-versal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-versal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-versal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-versal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-versal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-versal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-versal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-versal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-versal-tutorial/tutorial_general_203.png

