---
title: "Didacticiel : intégration d’Azure Active Directory à IBM Kenexa Survey Enterprise | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et IBM Kenexa Survey Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 929ae8b780c0e18074832adfefb9a7674097fd08
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Didacticiel : intégration d’Azure Active Directory à IBM Kenexa Survey Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer IBM Kenexa Survey Enterprise à Azure Active Directory (Azure AD).

L’intégration du logiciel IBM Kenexa Survey Enterprise à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à IBM Kenexa Survey Enterprise.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à IBM Kenexa Survey Enterprise en utilisant une authentification unique (SSO) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour plus d’informations sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à IBM Kenexa Survey Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement IBM Kenexa Survey Enterprise pour lequel l’authentification unique est activée

> [!NOTE]
> Lorsque vous testez les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

* Ajout de l’application IBM Kenexa Survey Enterprise à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Ajouter l’application IBM Kenexa Survey Enterprise à partir de la galerie
Pour configurer l’intégration d’IBM Kenexa Survey Enterprise à Azure AD, vous devez l’ajouter à votre liste d’applications SaaS managées à partir de la galerie.

Pour ajouter IBM Kenexa Survey Enterprise à partir de la galerie, procédez comme suit :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), cliquez sur le bouton **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une application, cliquez sur le bouton **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **IBM Kenexa Survey Enterprise**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Dans la liste des résultats, sélectionnez **IBM Kenexa Survey Enterprise**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![IBM Kenexa Survey Enterprise dans la liste des résultats](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec IBM Kenexa Survey Enterprise au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit identifier l’utilisateur équivalent à l’utilisateur Azure AD dans IBM Kenexa Survey Enterprise. En d’autres termes, Azure AD doit établir un lien entre un utilisateur Azure AD et un utilisateur associé dans IBM Kenexa Survey Enterprise.

Pour établir la relation de lien, assignez la valeur du **nom d’utilisateur** dans IBM Kenexa Survey Enterprise en tant que de **nom d’utilisateur** dans Azure AD.

Pour configurer et tester l’authentification unique Azure AD auprès d’IBM Kenexa Survey Enterprise, reportez-vous aux blocs de construction des deux sections suivantes.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique (SSO) Azure AD dans le portail Azure et la configurer dans votre application IBM Kenexa Survey Enterprise en procédant comme suit :

1. Dans le portail Azure, sur la page d’intégration de l’application **IBM Kenexa Survey Enterprise**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique dans IBM Kenexa Survey Enterprise][4]

2. Dans la boîte de dialogue **Authentification unique**, dans la zone **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique (SSO).
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. Dans la section **Domaine et URL IBM Kenexa Survey Enterprise**, procédez comme suit :

    ![Informations d’authentification unique de domaine et d’URL d’IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. Dans la zone de texte **Identificateur**, entrez un URL au format suivant : `https://surveys.kenexa.com/<companycode>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas réelles. Mettez-les à jour avec l’identificateur et l’URL de réponse réels. Pour obtenir les valeurs réelles, contactez l’[équipe de support technique d’IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

4. Sous **Certificat de signature SAML**, cliquez sur **Certificat (en base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat (en base64)](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    L’application IBM Kenexa Survey Enterprise s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de votre jeton SAML. La valeur de la revendication d’identificateur d’utilisateur dans la réponse doit correspondre à l’ID SSO configuré dans le système Kenexa. Pour mapper l’identificateur d’utilisateur approprié dans votre organisation en tant que protocole IDP (Internet Datagram Protocol) d’authentification unique, collaborez avec l’[équipe de support technique IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    Par défaut, AD Azure définit l’identificateur d’utilisateur en tant que valeur de nom d’utilisateur principal (UPN). Vous pouvez modifier cette valeur sous l’onglet **Attribut**, comme illustré dans la capture d’écran suivante. L’intégration fonctionne uniquement une fois le mappage correctement terminé.
    
    ![Boîte de dialogue Attributs de l’utilisateur](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Cliquez sur **Enregistrer**.

    ![Bouton Enregistrer de la fenêtre Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Pour ouvrir la fenêtre **Configurer l’authentification**, sous **Configuration d’IBM Kenexa Survey Enterprise**, cliquez sur **Configurer IBM Kenexa Survey Enterprise**. 
 
    ![Lien Configurer IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Copiez les valeurs **URL de déconnexion**, **ID d’entité SAML** et **URL du service d’authentification unique SAML** à partir de la section **Référence rapide**.

8. Dans la fenêtre **Configurer l’authentification**, sous **Référence rapide**, copiez les valeurs **URL de déconnexion**, **ID d’entité SAML** et **URL du service d’authentification unique SAML**.

9. Pour configurer l’authentification unique côté **IBM Kenexa Survey Enterprise**, envoyez les valeurs **Certificat (en base64)**, **URL de déconnexion**, **ID d’entité SAML** et **URL du service d’authentification unique SAML** téléchargées à l’[équipe de support technique IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com) pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** à la fin. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez la [documentation incorporée d’Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure en procédant comme suit :

![Créer un utilisateur de test Azure AD][100]

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.
 
    ![Bouton Ajouter](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Créer un utilisateur de test IBM Kenexa Survey Enterprise

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans IBM Kenexa Survey Enterprise. 

Pour créer des utilisateurs dans le système IBM Kenexa Survey Enterprise et mapper l’ID SSO pour ceux-ci, vous pouvez collaborer avec l’[équipe de support technique IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Par ailleurs, cette ID SSO doit être mappé à la valeur d’identificateur d’utilisateur fournie par Azure AD. Vous pouvez modifier ce paramètre par défaut sous l’onglet **Attribut**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à IBM Kenexa Survey Enterprise.

![Attribuer le rôle d’utilisateur][200] 

Pour affecter l’utilisateur Britta Simon à IBM Kenexa Survey Enterprise, procédez comme suit :

1. Dans le portail Azure, ouvrez la vue **Applications**, accédez à la vue **Répertoire**, sélectionnez **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Liens « Applications d’entreprise » et « Toutes les applications »][201] 

2. Dans la liste **Applications**, sélectionnez **IBM Kenexa Survey Enterprise**.

    ![Lien IBM Kenexa Survey Enterprise dans la liste Applications](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. Dans le volet gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Cliquez sur le bouton **Ajouter** puis, dans le volet **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une affectation][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, dans la liste **Utilisateurs**, sélectionnez **Britta Simon**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette **IBM Kenexa Survey Enterprise** dans le panneau d’accès, vous devez être connecté automatiquement à votre application IBM Kenexa Survey Enterprise.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
