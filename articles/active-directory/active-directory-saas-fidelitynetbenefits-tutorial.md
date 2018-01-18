---
title: "Didacticiel : Intégration d’Azure Active Directory à Fidelity NetBenefits | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fidelity NetBenefits."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: 007d3c894731560423e2dde0572793a4282a4654
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Didacticiel : Intégration d’Azure Active Directory à Fidelity NetBenefits

Dans ce didacticiel, vous allez apprendre à intégrer Fidelity NetBenefits à Azure Active Directory (Azure AD).

L’intégration de Fidelity NetBenefits à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Fidelity NetBenefits.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Fidelity NetBenefits (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Fidelity NetBenefits, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Fidelity NetBenefits pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Fidelity NetBenefits à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Ajout de Fidelity NetBenefits à partir de la galerie
Pour configurer l’intégration de Fidelity NetBenefits à Azure AD, vous devez ajouter Fidelity NetBenefits à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Fidelity NetBenefits depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Fidelity NetBenefits**, sélectionnez **Fidelity NetBenefits** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Fidelity NetBenefits dans la liste des résultats](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Fidelity NetBenefits au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Fidelity NetBenefits équivalent. En d’autres termes, une relation de lien entre un utilisateur Azure AD et l’utilisateur Fidelity NetBenefits associé doit être établie.

Dans Fidelity NetBenefits, le mappage de **l’utilisateur** doit être effectué avec **l’utilisateur Azure AD** afin d’établir une relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec Fidelity NetBenefits, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Fidelity NetBenefits](#create-a-fidelity-netbenefits-test-user)** pour disposer d’un équivalent à Britta Simon dans Fidelity NetBenefits lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure, puis configurer l’authentification unique dans votre application Fidelity NetBenefits.

**Pour configurer l’authentification unique Azure AD avec Fidelity NetBenefits, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Fidelity NetBenefits**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. Dans la section **Domaine et URL Fidelity NetBenefits**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL :

    Pour l’environnement de test : `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Pour l’environnement de production : `urn:sp:fidelity:geninbndnbparts20`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant :

    Pour l’environnement de test : `https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    Pour l’environnement de production : `https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
4. L’application Fidelity NetBenefits attend les assertions SAML dans un format spécifique. Nous avons mappé **l’identificateur d’utilisateur** avec **user.userprincipalname**. Vous pouvez le mapper avec **employeeid** ou toute autre revendication utilisée par votre organisation comme **identificateur d’utilisateur**. La capture d’écran suivante montre un exemple.

    ![Attribut de Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Fidelity NetBenefits prend en charge la fédération statique et dynamique. Par statique, nous entendons que l’application n’utilisera pas l’attribution d’utilisateurs juste-à-temps d’après SAML. Par dynamique, nous entendons qu’elle prendra en charge l’attribution d’utilisateurs juste-à-temps. Pour utiliser l’attribution juste-à-temps, les clients doivent ajouter d’autres revendications dans Azure AD comme la date de naissance de l’utilisateur, etc. Ces détails sont fournis par [l’équipe de support de Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) et doivent activer la fédération dynamique pour votre instance.
    
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Fidelity NetBenefits**, cliquez sur **Configurer Fidelity NetBenefits** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

7. Pour configurer l’authentification unique côté **Fidelity NetBenefits**, vous devez envoyer le fichier **XML des métadonnées** téléchargé, **l’URL du service d’authentification unique SAML** et **l’ID d’entité SAML** à [l’équipe de support de Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Créer un utilisateur de test Fidelity NetBenefits

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Fidelity NetBenefits. Dans le cadre d’une fédération statique, veuillez collaborer avec [l’équipe de support de Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) pour créer des utilisateurs dans la plateforme Fidelity NetBenefits. Ces utilisateurs doivent être créés et activés avant d’utiliser l’authentification unique. 

Dans le cadre d’une fédération dynamique, les utilisateurs sont créés à l’aide de l’attribution juste-à-temps. Pour l’utiliser, les clients doivent ajouter d’autres revendications dans Azure AD comme la date de naissance de l’utilisateur, etc. Ces détails sont fournis par [l’équipe de support de Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) et doivent activer la fédération dynamique pour votre instance.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fidelity NetBenefits.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Fidelity NetBenefits, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Fidelity NetBenefits**.

    ![Lien Fidelity NetBenefits dans la liste des applications](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Fidelity NetBenefits dans le volet d’accès, vous êtes automatiquement connecté à l’application Fidelity NetBenefits.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_203.png

