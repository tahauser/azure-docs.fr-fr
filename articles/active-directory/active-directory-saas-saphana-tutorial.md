---
title: "Didacticiel : intégration d’Azure Active Directory à SAP HANA | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 02c751b5d9e6f13c81b9c957cd6689c1fc744a07
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Didacticiel : Intégration d’Azure Active Directory à SAP HANA

Dans ce didacticiel, vous allez apprendre à intégrer SAP HANA à Azure Active Directory (Azure AD).

Quand vous intégrez SAP HANA à Azure AD, vous bénéficiez des avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SAP HANA.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SAP HANA avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec SAP HANA, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP HANA pour lequel l’authentification unique est activée
- Une instance HANA en cours d’exécution sur une IaaS publique, locale, sur une machine virtuelle Azure ou sur une grande instance SAP dans Azure
- L’interface web d’administration XSA ainsi que HANA Studio, installés sur l’instance HANA

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production de SAP HANA pour tester les étapes de ce didacticiel. Testez d’abord l’intégration dans l’environnement de développement ou l’environnement de préproduction de l’application, puis passez à l’environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Obtenez [un mois d’essai gratuit](https://azure.microsoft.com/pricing/free-trial/) d’Azure AD si vous n’avez pas encore un environnement d’essai d’Azure AD.

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP HANA à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-sap-hana-from-the-gallery"></a>Ajouter SAP HANA à partir de la galerie
Pour configurer l’intégration de SAP HANA à Azure AD, vous ajoutez SAP HANA à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SAP HANA à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter la nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SAP HANA**. Ensuite, sélectionnez **SAP HANA** à partir du volet de résultats. Enfin, sélectionnez le bouton **Ajouter** pour ajouter l’application. 

    ![Nouvelle application](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP HANA, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP HANA équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAP HANA associé.

Dans SAP HANA, donnez à la valeur **Username** la même valeur que le **nom d’utilisateur** dans Azure AD. Cette étape établit le lien entre les deux utilisateurs.

Pour configurer et tester l’authentification unique Azure AD avec SAP HANA, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test SAP HANA](#creating-a-sap-hana-test-user) pour avoir un équivalent de Britta Simon dans SAP HANA lié à la représentation Azure AD de l’utilisateur.
4. [Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#testing-single-sign-on) pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SAP HANA.

**Pour configurer l’authentification unique Azure AD avec SAP HANA, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SAP HANA**, sélectionnez **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Authentification basée sur SAML**, sélectionnez **Mode**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Dans la section **Domaine et URL SAP HANA**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Dans la zone **Identificateur**, tapez `HA100`. 

    b. Dans la zone **URL de réponse**, tapez une URL au format suivant : `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SAP HANA](https://cloudplatform.sap.com/contact.html). 

4. Dans la section **Certificat de signature SAML**, sélectionnez **XML des métadonnées**. Ensuite, enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Si le certificat n’est pas actif, activez-le en cochant la case **Activer le nouveau certificat** dans Azure AD. 

5. L’application SAP HANA attend les assertions SAML dans un format spécifique. La capture d’écran suivante présente un exemple de ce format. 

    Ici, nous avons mappé **l’identificateur d’utilisateur** avec la fonction **ExtractMailPrefix()** de **user.mail**. Cela donne la valeur de préfixe de l’adresse e-mail de l’utilisateur, qui est l’ID d’utilisateur unique. Cet ID d’utilisateur est envoyé à l’application SAP HANA pour chaque réponse correcte.

    ![Configurer l’authentification unique](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, effectuez les étapes suivantes :

    a. Dans la liste déroulante **Identificateur de l’utilisateur**, sélectionnez **ExtractMailPrefix**.
    
    b. Dans la liste déroulante **E-mail**, sélectionnez **user.mail**.

7. Sélectionnez le bouton **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Pour configurer l’authentification unique côté SAP HANA, connectez-vous à votre **console web HANA XSA** en accédant au point de terminaison HTTPS correspondant.

    > [!NOTE]
    > Dans la configuration par défaut, l’URL redirige la requête vers un écran d’ouverture de session, où les informations d’identification d’un utilisateur de la base de données SAP HANA agréé sont nécessaires. L’utilisateur qui se connecte doit avoir les autorisations nécessaires pour effectuer des tâches d’administration SAML.

9. Dans l’Interface web XSA, accédez à **SAML Identity Provider** (Fournisseur d’identité SAML). Ensuite, sélectionnez le bouton **+** en bas de l’écran pour afficher le volet **Add Identity Provider Info** (Ajouter les informations du fournisseur d’identité). Ensuite, effectuez les étapes suivantes :

    ![Ajouter un fournisseur d’identité](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. Dans le volet **d’ajout des informations sur le fournisseur d’identité**, collez le contenu du fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure dans la zone **Métadonnées**.

    ![Paramètres d’ajout d’un fournisseur d’identité](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Si le contenu du document XML est valide, le processus d’analyse extrait les informations requises pour les champs **Subject, Entity ID et Issuer** (Sujet, ID d’entité et Émetteur) dans la zone d’écran **General data** (Données générales). Il extrait également les informations nécessaires pour les champs d’URL dans la zone d’écran **Destination**, par exemple, les champs **Base URL et SingleSignOn URL (*)** (URL de base et URL d’authentification unique).

    ![Paramètres d’ajout d’un fournisseur d’identité](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Dans la zone **Nom** de la zone d’écran **General Data** (Données générales), entrez un nom pour le nouveau fournisseur d’identité d’authentification unique SAML.

    > [!NOTE]
    > Le nom du fournisseur d’identité SAML est obligatoire et doit être unique. Il apparaît dans la liste des fournisseurs d’identité SAML disponibles qui s’affiche quand vous sélectionnez SAML comme méthode d’authentification pour les applications SAP HANA XS à utiliser. Par exemple, vous pouvez faire cela dans la zone d’écran **Authentication** (Authentification) de l’outil d’administration d’artefact XS.

10. Sélectionnez **Save** (Enregistrer) pour enregistrer les détails du fournisseur d’identité SAML et ajouter le nouveau fournisseur d’identité SAML à la liste des fournisseurs d’identité SAML connus.

    ![Bouton Enregistrer](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. Dans Studio HANA, dans les propriétés système de l’onglet **Configuration**, filtrez les paramètres en fonction de **saml**. Ensuite, ajustez **assertion_timeout** en remplaçant **10 s** par **120 s**.

    ![Paramètre assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com) pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez [Documentation incorporée Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du **portail Azure**, sélectionnez l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**. Puis sélectionnez **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue.
 
    ![Bouton Ajouter](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur**, tapez l’**adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez le mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-sap-hana-test-user"></a>Créer un utilisateur de test SAP HANA

Pour permettre aux utilisateurs Azure AD de se connecter à SAP HANA, vous devez les provisionner dans SAP HANA.
SAP HANA prend en charge le provisionnement juste-à-temps, qui est activé par défaut.

Si vous avez besoin de créer un utilisateur manuellement, effectuez les étapes suivantes :

>[!NOTE]
>Vous pouvez changer l’authentification externe dont se sert l’utilisateur. Il peut s’authentifier auprès d’un système externe, tel que Kerberos. Pour plus d’informations sur les identités externes, contactez votre [administrateur de domaine](https://cloudplatform.sap.com/contact.html).

1. Ouvrez [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) en tant qu’administrateur, puis activez l’utilisateur de base de données pour l’authentification unique SAML.

    ![Créer un utilisateur](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Cochez la case invisible à gauche de **SAML**, puis sélectionnez le lien **Configurer**.

3. Sélectionnez **Ajouter** pour ajouter le fournisseur d’identité SAML.  Sélectionnez le fournisseur d’identité SAML approprié, puis sélectionnez **OK**.

4. Ajouter **l’identité externe** (dans ce cas, BrittaSimon) ou choisissez **Quelconque**. Sélectionnez ensuite **OK**.

    >[!Note]
    >Si la case **Quelconque** n’est pas cochée, le nom d’utilisateur dans HANA doit correspondre exactement au nom de l’utilisateur dans le nom d’utilisateur principal avant le suffixe de domaine. (Par exemple, BrittaSimon@contoso.com deviendrait BrittaSimon dans HANA.)

5. À des fins de test, affectez tous les rôles **XS** à l’utilisateur.

    ![Attribution de rôles](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Vous devez donner les autorisations qui sont appropriées pour vos cas d’usage uniquement.

6. Enregistrez l’utilisateur.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP HANA.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à SAP HANA, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications. Accédez à la vue d’annuaire, puis à **Applications d’entreprise**. Sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP HANA**.

    ![Affecter des utilisateurs](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Sélectionnez le bouton **Ajouter**. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Attribuer** dans la boîte de dialogue **Ajouter une attribution**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette SAP HANA dans le panneau d’accès, vous devez être connecté automatiquement à votre application SAP HANA.
Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

