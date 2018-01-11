---
title: "Didacticiel : Intégration d’Azure Active Directory à SilkRoad Life Suite | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SilkRoad Life Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Didacticiel : Intégration d’Azure Active Directory à SilkRoad Life Suite

Dans ce didacticiel, vous allez apprendre à intégrer SilkRoad Life Suite à Azure Active Directory (Azure AD).

L’intégration de SilkRoad Life Suite à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SilkRoad Life Suite.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SilkRoad Life Suite (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SilkRoad Life Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SilkRoad Life Suite pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SilkRoad Life Suite à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Ajout de SilkRoad Life Suite à partir de la galerie
Pour configurer l’intégration de SilkRoad Life Suite dans Azure AD, vous devez ajouter SilkRoad Life Suite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SilkRoad Life Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SilkRoad Life Suite**, sélectionnez **SilkRoad Life Suite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SilkRoad Life Suite dans la liste des résultats](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SilkRoad Life Suite, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur SilkRoad Life Suite équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SilkRoad Life Suite associé doit être établi.

Dans SilkRoad Life Suite, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SilkRoad Life Suite, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test SilkRoad Life Suite](#create-a-silkroad-life-suite-test-user)** pour avoir un équivalent de Britta Simon dans SilkRoad Life Suite qui soit lié à la représentation de l’utilisateur dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SilkRoad Life Suite.

**Pour configurer l’authentification unique Azure AD avec SilkRoad Life Suite, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **SilkRoad Life Suite**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Dans la section **Domaine et URL SilkRoad Life Suite**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SilkRoad Life Suite](https://www.silkroad.com/locations/). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de SilkRoad Life Suite**, cliquez sur **Configurer SilkRoad Life Suite** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Connectez-vous à votre site d’entreprise SilkRoad en tant qu’administrateur. 
 
    >[!NOTE] 
    > Pour obtenir l’accès à l’application d’authentification SilkRoad Life Suite pour configurer la fédération avec Microsoft Azure AD, contactez le support technique SilkRoad ou votre représentant SilkRoad Services.

8. Accédez à **Service Provider** (Fournisseur de services), puis cliquez sur **Federation Details** (Informations sur la fédération). 
   
    ![Authentification unique Azure AD][10]

9. Cliquez sur **Download Federation Metadata**(Télécharger les métadonnées de fédération), puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
    ![Authentification unique Azure AD][11] 

10. Dans votre application **SilkRoad**, cliquez sur **Authentication Sources** (Sources d’authentification).
   
    ![Authentification unique Azure AD][12] 

11. Cliquez sur **Add Authentication Source**(Ajouter une source d’authentification). 
   
    ![Authentification unique Azure AD][13] 

12. Dans la section **Add Authentication Source** (Ajouter une source d’authentification), procédez comme suit : 
   
    ![Authentification unique Azure AD][14]
  
    a. Sous **Option 2 - Metadata File** (Option 2 - Fichier de métadonnées), cliquez sur **Browse** (Parcourir) pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.
  
    b. Cliquez sur **Create Identity Provider using File Data**.

13. Dans la section **Authentication Sources** (Sources d’authentification), cliquez sur **Edit** (Modifier). 
    
     ![Authentification unique Azure AD][15] 

14. Dans la boîte de dialogue **Edit Authentication Source** (Modifier la source d’authentification), procédez comme suit : 
    
     ![Authentification unique Azure AD][16] 

    a. Pour l’option **Enabled**, sélectionnez **Yes**.

    b. Dans la zone de texte **EntityId** (ID d’entité), collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure.
   
    c. Dans la zone de texte **IdP Description** (Description IdP), entrez une description de votre configuration (par exemple : *Authentification unique Azure AD*).

    d. Dans la zone de texte **Metadata File** (Fichier de métadonnées), chargez le fichier de **métadonnées** que vous avez téléchargé à partir du portail Azure.
  
    e. Dans la zone de texte **IdP Name** (Nom IdP), entrez un nom spécifique de votre configuration (par exemple : *Azure SP*).
  
    f. Dans la zone de texte **Logout Service URL** (URL du service de déconnexion), collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

    g. Dans la zone de texte **Sign-on service URL** (URL du service d’authentification), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    h. Cliquez sur **Enregistrer**.

15. Désactivez toutes les autres sources d’authentification. 
    
     ![Authentification unique Azure AD][17]

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Créer un utilisateur de test SilkRoad Life Suite

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SilkRoad Life Suite. Collaborez avec [l’équipe de support technique SilkRoad Life Suite](https://www.silkroad.com/locations/) pour ajouter des utilisateurs dans la plateforme SilkRoad Life Suite. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SilkRoad Life Suite.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à SilkRoad Life Suite, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SilkRoad Life Suite**.

    ![Lien SilkRoad Life Suite dans la liste des applications](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SilkRoad Life Suite dans le panneau d’accès, vous devez être connecté automatiquement à votre application SilkRoad Life Suite.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
