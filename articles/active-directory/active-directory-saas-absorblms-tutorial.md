---
title: "Didacticiel : Intégration d’Azure Active Directory à Absorb LMS | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 4231b5e72ea0852de344e7484bdf20997243cee6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Didacticiel : Intégration d’Azure Active Directory avec Absorb LMS

Dans ce didacticiel, vous allez apprendre à intégrer Absorb LMS avec Azure Active Directory (Azure AD).

L’intégration d’Absorb LMS avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Absorb LMS.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Absorb LMS (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour plus d’informations sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Absorb LMS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Absorb LMS pour lequel l’authentification unique est activée

> [!NOTE]
> Nous vous recommandons de ne pas utiliser d’environnement de production pour ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout d’Absorb LMS à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-absorb-lms-from-the-gallery"></a>Ajouter Absorb LMS à partir de la galerie
Pour configurer l’intégration de Absorb LMS avec Azure AD, ajoutez Absorb LMS à partir de la galerie à votre liste d’applications SaaS gérées.

Pour ajouter Absorb LMS à partir de la galerie, procédez comme suit :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise][2]
    
3. Pour ajouter une application, cliquez sur le bouton **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Absorb LMS**, sélectionnez **Absorb LMS** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter**.

    ![Absorb LMS dans la liste des résultats](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Absorb LMS, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Absorb LMS équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur correspondant dans Absorb LMS.

Vous établissez cette relation de liaison en affectant la valeur *nom d’utilisateur* dans Azure AD comme la valeur *nom d’utilisateur* dans Absorb LMS.

Pour configurer et tester l’authentification unique Azure AD avec Absorb LMS, suivez les indications des sections dans les cinq sections suivantes.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Absorb LMS.

Pour configurer l’authentification unique Azure AD avec Absorb LMS, procédez comme suit :

1. Dans le portail Azure, sur la page d’intégration de l’application **Absorb LMS**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, dans la zone **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Dans la section **Domaine et URL Absorb LMS**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL utilisant la syntaxe suivante : `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. Dans la zone de texte **URL de réponse**, tapez une URL utilisant la syntaxe suivante : `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Ces URL ne sont pas des valeurs réelles. Mettez-les à jour avec l’identificateur et les URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Absorb LMS](https://www.absorblms.com/support). 

4. Dans la section **Certificat de signature SAML**, au sein de la colonne **Télécharger** sélectionnez **Métadonnées XML**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat de signature](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de Absorb LMS**, sélectionnez **Configurer Absorb LMS** pour ouvrir la fenêtre **Configurer l’authentification**, puis copiez l’**URL de déconnexion** dans la **section de référence rapide.**

    ![Volet Configuration de Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. Dans une nouvelle fenêtre de navigateur web, connectez-vous au site de votre entreprise Absorb LMS en tant qu’administrateur.

8. Sélectionnez le bouton **Compte** en haut à droite. 

    ![Bouton Compte](./media/active-directory-saas-absorblms-tutorial/1.png)

9. Dans le volet Compte, sélectionnez **Paramètres du portail**.

    ![Lien Paramètres du portail](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Cliquez sur l’onglet **Utilisateurs** .

    ![L’onglet Utilisateurs](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Dans la page de configuration d’authentification unique, procédez comme suit :

    ![Page Configuration d’authentification unique](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Dans la zone **Mode**, sélectionnez **Identity Provider Initiated** (Initiée par le fournisseur d’identité).

    b. Dans le bloc-notes, ouvrez le certificat que vous avez téléchargé à partir du portail Azure. Supprimez les balises **-----BEGIN CERTIFICATE-----** et **-----END CERTIFICATE-----**. Puis, dans la zone **Clé**, collez le reste du contenu.
    
    c. Dans la zone **Propriété ID** , sélectionnez l’attribut que vous avez configuré comme identificateur d’utilisateur dans Azure AD. Par exemple, si *userPrincipalName* est sélectionné dans Azure AD, sélectionnez **Nom d’utilisateur**.

    d. Dans la zone **Login URL** (URL de connexion), collez l’**URL d’accès utilisateur** indiquée dans la page **Propriétés** du portail Azure.

    e. Dans **Logout URL** (URL de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée depuis la fenêtre **Configurer l’authentification** du portail Azure.

12. Réglez **Autoriser uniquement la connexion SSO** sur **On**.

    ![Activer/désactiver Autoriser uniquement la connexion SSO](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Sélectionnez **Enregistrer**.

> [!TIP]
> Vous pouvez lire une version concise de ces instructions sur le [Portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour plus d’informations, consultez la page [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, sélectionnez **Ajouter**.
 
    ![Bouton Ajouter](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez l’adresse de messagerie de Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur dans la zone **Mot de passe**.

    d. Sélectionnez **Créer**.

### <a name="create-an-absorb-lms-test-user"></a>Créer un utilisateur de test Absorb LMS

Les utilisateurs Azure AD désirant se connecter à Absorb LMS doivent être configurés dans Absorb LMS.  

Pour Absorb LMS, la configuration est une tâche manuelle.

Pour configurer un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Absorb LMS en tant qu’administrateur.

2. Dans le volet gauche, sélectionnez **Utilisateurs**.

    ![Lien Utilisateurs de Absorb LMS](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. Dans le volet **Utilisateurs**, sélectionnez **Utilisateurs**.

    ![Lien Utilisateurs](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. Dans la liste déroulante **Ajouter un nouveau**, sélectionnez **Utilisateur**.

    ![Liste déroulante Ajouter un nouveau](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Dans la page **Ajouter un utilisateur**, procédez comme suit :

    ![Page Ajouter un utilisateur](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. Dans la zone **Prénom**, tapez le prénom, par exemple **Britta**.

    b. Dans la zone **Nom**, tapez le nom de famille, par exemple **Simon**.
    
    c. Dans la zone **Nom d’utilisateur**, tapez le nom complet, par exemple **Britta Simon**.

    d. Dans la zone **Mot de passe**, saisissez le mot de passe de Britta Simon.

    e. Dans la zone **Confirmer le mot de passe**, saisissez de nouveau le mot de passe.
    
    f. Configurez le réglage **Est actif** sur **Actif**.  

6. Sélectionnez **Enregistrer**.
 
### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Absorb LMS.

![Attribuer le rôle utilisateur][200]

Pour affecter l’utilisateur Britta Simon à Absorb LMS, procédez comme suit :

1. Dans le portail Azure, ouvrez la vue Applications, accédez à la vue Répertoire, puis sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Lien Toutes les applications][201] 

2. Dans la liste **Applications**, sélectionnez **Absorb LMS**.

    ![Lien Absorb LMS dans la liste des applications](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Sélectionnez **Ajouter** puis, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, dans la liste **Utilisateurs**, sélectionnez **Britta Simon**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Dans le volet d’accès, la sélection de la mosaïque **Absorb LMS** vous connecte automatiquement à votre application Absorb LMS. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

