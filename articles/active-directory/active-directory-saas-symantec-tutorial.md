---
title: "Didacticiel : Intégration d’Azure Active Directory à Symantec Web Security Service (WSS) | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Symantec Web Security Service (WSS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 35427941a084e6750d66ccd3e135d7eef1767c6c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Didacticiel : Intégration d’Azure Active Directory à Symantec Web Security Service (WSS)

Dans ce didacticiel, vous allez apprendre à intégrer votre compte Symantec Web Security Service (WSS) à votre compte Azure Active Directory (Azure AD) afin que WSS puisse authentifier un utilisateur final approvisionné dans Azure AD à l’aide de l’authentification SAML et appliquer les règles de stratégie au niveau de l’utilisateur ou du groupe.

L’intégration de Symantec Web Security Service (WSS) à Azure AD vous fait bénéficier des avantages suivants :

- Gérer tous les groupes et utilisateurs finaux utilisés par votre compte WSS à partir de votre portail Azure AD. 

- Autoriser les utilisateurs finaux à s’authentifier sur WSS à l’aide de leurs informations d’identification Azure AD.

- Activer l’application des règles de stratégie au niveau de l’utilisateur et du groupe définies dans votre compte WSS.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Symantec Web Security Service (WSS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un compte Symantec Web Security Service (WSS)

> [!NOTE]
> Pour tester les étapes de ce didacticiel, il est déconseillé d’utiliser un compte WSS qui est actuellement utilisé à des fins de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas le compte WSS qui est actuellement utilisé à des fins de production pour ce test, sauf si c’est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous allez configurer votre instance Azure AD pour activer l’authentification unique sur WWS à l’aide des informations d’identification de l’utilisateur final définies dans votre compte Azure AD.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de l’application Symantec Web Security Service (WSS) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Ajout de Symantec Web Security Service (WSS) à partir de la galerie
Pour configurer l’intégration de Symantec Web Security Service (WSS) à Azure AD, vous devez ajouter Symantec Web Security Service (WSS) à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Symantec Web Security Service (WSS) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Symantec Web Security Service (WSS)**, sélectionnez **Symantec Web Security Service (WSS)** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Symantec Web Security Service (WSS) dans la liste des résultats](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Symantec Web Security Service (WSS) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Symantec Web Security Service (WSS) équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Symantec Web Security Service (WSS) associé doit être établie.

Dans Symantec Web Security Service (WSS), affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD auprès de Symantec Web Security Service (WSS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Symantec Web Security Service (WSS)](#create-a-symantec-web-security-service-wss-test-user)** pour avoir dans Symantec Web Security Service (WSS) un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Symantec Web Security Service (WSS).

**Pour configurer l’authentification unique Azure AD avec Symantec Web Security Service (WSS), procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Symantec Web Security Service (WSS)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. Dans la section **Domaine et URL Symantec Web Security Service (WSS)** section, procédez comme suit :

    ![Informations d’authentification unique Domaine et URL Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. Dans la zone de texte **Identificateur**, saisissez l’URL : `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL : `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Veuillez contacter l’[équipe de support technique de Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) si les valeurs **Identificateur** et **URL de réponse** ne fonctionnent pas pour une raison quelconque.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Pour configurer l’authentification unique côté Symantec Web Security Service (WSS), consultez la documentation en ligne de WSS. Le fichier **XML de métadonnées** devra être importé dans le portail WSS. Contactez l’[équipe de support Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) si vous avez besoin d’aide pour configurer le portail WSS.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Créer un utilisateur de test Symantec Web Security Service (WSS)

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Symantec Web Security Service (WSS). Le nom d’utilisateur final correspondante peut être créé manuellement dans le portail WSS ou vous pouvez attendre que les utilisateurs/groupes approvisionnés dans Azure AD se synchronisent dans le portail WSS après quelques minutes (environ 15 minutes). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. L’adresse IP publique de l’ordinateur de l’utilisateur final, qui sera utilisée pour parcourir les sites web, doit également être configurée dans le portail Symantec Web Security Service (WSS).

> [!NOTE]
> [Cliquez ici](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) pour obtenir l’adresse IP publique de votre ordinateur.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Symantec Web Security Service (WSS).

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Symantec Web Security Service (WSS), procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Symantec Web Security Service (WSS)**.

    ![Lien Symantec Web Security Service (WSS) dans la liste des applications](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la fonctionnalité d’authentification unique maintenant que vous avez configuré votre compte WSS pour utiliser votre système Azure AD pour l’authentification SAML.

Une fois que vous avez configuré votre navigateur web pour le trafic de proxy WSS, lorsque vous ouvrez votre navigateur web et que vous essayez d’accéder à un site, vous serez redirigé vers la page d’ouverture de session Azure. Entrez les informations d’identification de l’utilisateur final de test qui a été approvisionné dans Azure AD (autrement dit, BrittaSimon) et le mot de passe correspondant. Une fois authentifié, vous pourrez accéder au site web que vous avez choisi. Si vous devez créer une règle de stratégie côté WSS pour empêcher BrittaSimon d’accéder à un site spécifique, vous devriez voir la page de blocage WSS lorsque vous tentez d’accéder à ce site en tant que BrittaSimon.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

