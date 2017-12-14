---
title: "Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: fff906913b637c47d394f9127983a08f96d568e4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Atlassian Cloud à Azure Active Directory (Azure AD).

L’intégration d’Atlassian Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Atlassian Cloud.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Atlassian Cloud (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Atlassian Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Pour pouvoir activer l’authentification unique SAML pour les produits Atlassian Cloud, vous devez installer le Gestionnaire d’identités. En savoir plus sur le [Gestionnaire d’identités]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Atlassian Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Ajout d’Atlassian Cloud à partir de la galerie
Pour configurer l’intégration d’Atlassian Cloud à Azure AD, vous devez ajouter Atlassian Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Atlassian Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Atlassian Cloud**, sélectionnez **Atlassian Cloud** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Atlassian Cloud dans la liste des résultats](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Atlassian Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Atlassian Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Atlassian Cloud associé doit être établie.

Dans Atlassian Cloud, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique avec Azure AD avec Atlassian Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Atlassian Cloud](#create-an-atlassian-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Atlassian Cloud lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Atlassian Cloud.

**Pour configurer l’authentification unique Azure AD avec Atlassian Cloud, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Atlassian Cloud**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Dans la section **Domaine et URL Atlassian Cloud**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par IDP** :

    ![Informations d’authentification unique dans Domaine et URL de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. Dans la zone de texte **Identificateur**, saisissez l’URL : `https://id.atlassian.com/login`
    
    b. Dans la zone de texte **URL de réponse**, tapez l’URL : `https://id.atlassian.com/login/saml/acs`

    c. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<instancename>.atlassian.net`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous pouvez obtenir ces valeurs dans l’écran de configuration de SAML pour Atlassian Cloud, qui est expliqué plus loin dans le didacticiel.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration d’Atlassian Cloud**, cliquez sur **Configurer Atlassian Cloud**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration d’Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Pour configurer l’authentification unique de votre application, connectez-vous au portail Atlassian à l’aide des droits d’administrateur.

9. Accédez à **Atlassian Site Administration** (Administration de site Atlassian) > **Organizations & Security** (Organisations & Sécurité). Si vous ne l’avez pas déjà fait, créez et nommez votre organisation. Dans le volet de navigation gauche, cliquez sur **Domains**  (Domaines).

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Sélectionnez comment vous voulez vérifier votre domaine : **DNS** ou **HTTPS**.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. Pour la vérification du DNS, sélectionnez l’onglet **DNS** sur la page **Domains** (Domaines) et procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Cliquez sur **Copy** (Copier) pour copier la valeur pour votre enregistrement TXT.

    b. Dans votre DNS, recherchez la page des paramètres pour ajouter un nouvel enregistrement

    c. Sélectionnez l’option d’ajout d’un nouvel enregistrement et collez la valeur que vous avez copiée depuis la page **Domains** (Domaines) dans le champ **Value** (Valeur). Votre DNS peut également l’appeler **Answer** (Réponse) ou **Description**.

    d. Votre enregistrement DNS peut aussi inclure les champs suivants :
    
    * **Type d’enregistrement** : entrez **TXT**
    * **Name/Host/Alias** (Nom /Hôte/Alias) : laissez la valeur par défaut (@ ou vide)
    * **Time to Live (TTL)** (Durée de vie) : entrez **86400**
    
    e.  Enregistrez l’enregistrement.

12. Revenez à la **page Domains** (Domaines) dans l’administration de l’organisation, puis cliquez sur le bouton **Verify domain** (Vérifier le domaine). Entrez votre nom de domaine dans la fenêtre contextuelle, puis cliquez sur le bouton **Verify domain** (Vérifier le domaine).

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > Jusqu’à 72 heures peuvent être nécessaires pour que les modifications de l’enregistrement TXT prennent effet : vous ne savez donc pas immédiatement si la vérification de votre domaine a réussi. Consultez votre page **Domains** (Domaines) dès que vous avez effectué ces étapes pour connaître l’état de la vérification. Vous voyez l’écran suivant avec l’état mis à jour à **VÉRIFIÉ**.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. Pour la vérification HTTPS, sélectionnez l’onglet **HTTPS** sur la page **Domaines** et procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Cliquez sur **Download file** (Télécharger le fichier)pour télécharger le fichier HTML.

    b.  Chargez le fichier HTML dans le répertoire racine de votre domaine.

14. Revenez à la **page Domains** (Domaines) dans l’administration de l’organisation, puis cliquez sur le bouton **Verify domain** (Vérifier le domaine). Entrez votre **nom de domaine** dans la fenêtre contextuelle, puis cliquez sur le bouton **Verify domain** (Vérifier le domaine).

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Si le processus de vérification peut localiser le fichier que vous avez chargé dans le répertoire racine, l’état du domaine passe à **Verified** (Vérifié).

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Pour plus d’informations, reportez-vous à la [documentation de vérification de domaine d’Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

16. Dans la barre de navigation de gauche, cliquez sur **SAML single sign-on** (Authentification unique SAML). Si vous n’avez pas déjà fait, abonnez-vous au Gestionnaire d’identités d’Atlassian.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. Dans la boîte de dialogue **Add SAML configuration** (Ajouter une configuration SAML), ajoutez les paramètres du fournisseur d’identité comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Dans la zone de texte **Identity Provider Entity ID** (ID d’entité du fournisseur d’identité), collez **l’ID d’entité SAML** que vous avez copié dans le portail Azure.

    b. Dans la zone de texte **Identity provider SSO URL** (URL d’authentification unique du fournisseur d’identité), collez **l’URL du service d’authentification unique SAML** que vous avez copiée dans le portail Azure.

    c. Ouvrez le certificat téléchargé dans le Bloc-notes, copiez les valeurs sans les lignes Begin et End, puis collez-les dans la zone **Public X509 certificate** (Certificat public X509).
    
    d. Cliquez sur **Save Configuration** (Enregistrer la configuration) pour enregistrer les paramètres.
     
18. Mettez à jour les paramètres Azure AD pour vérifier que vous avez configuré les URL correctes.
  
    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Copiez **l’ID d’identité SP** dans l’écran SAML et collez-le dans la zone **Identificateur** du portail Azure, dans la section **Domaine et URL** Atlassian Cloud.
    
    b. Copiez **l’URL du service consommateur d’assertion SP** dans l’écran SAML et collez-le dans la zone **URL de réponse** du portail Azure, dans la section **Domaine et URL** d’Atlassian Cloud.
    
    c. L’URL d’authentification est l’URL du client de votre Atlassian Cloud. 
    
19. Dans le portail Azure, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Créer un utilisateur de test Atlassian Cloud

Pour permettre aux utilisateurs Azure AD de se connecter à Atlassian Cloud, vous devez les attribuer dans Atlassian Cloud. Pour Atlassian Cloud, cette attribution est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Dans la section Administration de site du portail Atlassian, cliquez sur le bouton **Users** (Utilisateurs).

    ![Créer un utilisateur Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Cliquez sur le bouton **Invite user** (Inviter un utilisateur) pour créer un utilisateur dans Atlassian Cloud.

    ![Créer un utilisateur Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Entrez l’adresse e-mail de l’utilisateur dans le champ **Email address** (Adresse e-mail), puis affectez l’accès à l’application. 

    ![Créer un utilisateur Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Quand vous cliquez sur le bouton **Invite users** (Inviter des utilisateurs), un e-mail d’invitation est envoyé à l’utilisateur, et une fois l’invitation acceptée, l’utilisateur est actif dans le système. 

>[!NOTE] 
>Vous pouvez également créer des utilisateurs en bloc en cliquant sur le bouton **Créer en bloc** dans la section Utilisateurs.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Atlassian Cloud.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Atlassian Cloud, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Atlassian Cloud**.

    ![Lien Atlassian Cloud dans la liste des applications](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Atlassian Cloud dans le volet d’accès, vous devez être connecté automatiquement à votre application Atlassian Cloud.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

