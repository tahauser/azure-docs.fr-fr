---
title: "Didacticiel : Intégration d’Azure Active Directory à Veracode | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Veracode."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 270b373943ea72f1ddbee4f9c6fa4967ff85b861
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Didacticiel : Intégration d’Azure AD à Veracode

Dans ce didacticiel, vous allez apprendre à intégrer Veracode à Azure Active Directory (Azure AD).

L’intégration de Veracode dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Veracode.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Veracode (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Veracode, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Veracode pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Veracode à partir de la galerie
2. Configurer et tester l’authentification unique Azure AD

## <a name="add-veracode-from-the-gallery"></a>Ajouter Veracode à partir de la galerie
Pour configurer l’intégration de Veracode à Azure AD, vous devez ajouter Veracode à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Veracode à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Veracode**, sélectionnez **Veracode** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Veracode dans la liste des résultats](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Veracode avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Veracode équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Veracode associé doit être établie.

Dans Veracode, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Veracode, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Veracode](#create-a-veracode-test-user)** pour avoir un équivalent de Britta Simon dans Veracode lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Veracode.

**Pour configurer l’authentification unique Azure AD avec Veracode, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Veracode**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. Dans la section **Domaine et URL Veracode**, l’utilisateur n’aura à effectuer aucune étape, car l’application est déjà intégrée à Azure. 

    ![Configurer l’authentification unique](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. Cette section explique comment permettre aux utilisateurs de s’authentifier sur Veracode avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

    Votre application Veracode s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML** . La capture d’écran suivante montre un exemple :
    
    ![Attributs](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "Attributs")

6. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.Surname |
    | email |User.mail |
    
    a. Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **ajouter un attribut utilisateur**.
    
    ![Attributs](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "Attributs")
    
    ![Attributs](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "Attributs")
    
    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la zone de texte **Valeur de l’attribut** , sélectionnez la valeur d’attribut indiquée pour cette ligne.
    
    d. Cliquez sur **OK**.

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration de Veracode**, cliquez sur **Configurer Veracode**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Veracode en tant qu’administrateur.

10. Dans le menu situé en haut, cliquez sur **Settings** puis sur **Admin**.
   
    ![Administration](./media/active-directory-saas-veracode-tutorial/ic802911.png "Administration")

11. Cliquez sur l’onglet **SAML** .

12. Dans la section **Organization SAML Settings** , procédez comme suit :
   
    ![Administration](./media/active-directory-saas-veracode-tutorial/ic802912.png "Administration")
   
    a.  Dans la zone de texte **Issuer (Émetteur)**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure.
    
    b. Pour charger le certificat téléchargé à partir du portail Azure, cliquez sur **Choose File**.
   
    c. Sélectionnez **Enable Self Registration**.

13. Dans la section **Self Registration Settings**, procédez comme suit, puis cliquez sur **Save** :
   
    ![Administration](./media/active-directory-saas-veracode-tutorial/ic802913.png "Administration")
   
    a. Dans **New User Activation**, sélectionnez **No Activation Required**.
   
    b. Dans **User Data Updates**, sélectionnez **Preference Veracode User Data**.
   
    c. Dans **SAML Attribute Details**, sélectionnez les éléments suivants :
      * **Rôles d’utilisateur**
      * **Administrateur de la stratégie**
      * **Réviseur**
      * **Responsable de la sécurité**
      * **Responsable**
      * **Expéditeur**
      * **Créateur**
      * **Tous les types d’analyse**
      * **Appartenance aux équipes**
      * **Équipe par défaut**

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-veracode-test-user"></a>Créer un utilisateur de test Veracode
Pour se connecter à Veracode, les utilisateurs d’Azure AD doivent être approvisionnés dans Veracode. Dans le cas de Veracode, l’approvisionnement est une tâche automatique. Vous n’avez aucune opération à effectuer. Au besoin, les utilisateurs sont automatiquement créés lors de la première tentative d’authentification unique.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Veracode, pour approvisionner des comptes d’utilisateur Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Veracode.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Veracode, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Veracode**.

    ![Lien Veracode dans la liste des applications](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Veracode dans le volet d’accès, vous devez vous connecter automatiquement à votre application Veracode.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png

