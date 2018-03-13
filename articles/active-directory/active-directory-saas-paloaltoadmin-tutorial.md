---
title: "Didacticiel : Intégration d’Azure Active Directory avec Palo Alto Networks - Admin UI | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks - Admin UI."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 60430f08f54232db619efd054ca3a7d9a44f4cdc
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Didacticiel : Intégration d’Azure Active Directory avec Palo Alto Networks - Admin UI

Dans ce didacticiel, vous découvrez comment intégrer Palo Alto Networks - Admin UI avec Azure Active Directory (Azure AD).

L’intégration de Palo Alto Networks - Admin UI avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks - Admin UI.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Palo Alto Networks - Admin UI (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Palo Alto Networks - Admin UI, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un pare-feu Palo Alto Networks de nouvelle génération ou un dispositif Panorama (système de gestion centralisée pour les pare-feu)

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Palo Alto Networks - Admin UI depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Ajout de Palo Alto Networks - Admin UI depuis la galerie
Pour configurer l’intégration de Palo Alto Networks - Admin UI à Azure AD, vous devez ajouter Palo Alto Networks - Admin UI à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Palo Alto Networks - Admin UI à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Palo Alto Networks - Admin UI**, sélectionnez **Palo Alto Networks - Admin UI** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Palo Alto Networks - Admin UI dans la liste des résultats](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec Palo Alto Networks - Admin UI sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Palo Alto Networks - Admin UI équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Palo Alto Networks - Admin UI associé.

Dans Palo Alto Networks - Admin UI, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks - Admin UI, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créez un utilisateur de test Palo Alto Networks - Admin UI](#create-a-palo-alto-networks---admin-ui-test-user)** pour obtenir un équivalent de Britta Simon dans Palo Alto Networks - Admin UI lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et vous configurez l’authentification unique dans votre application Palo Alto Networks - Admin UI.

**Pour configurer l’authentification unique Azure AD avec Palo Alto Networks - Admin UI, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Palo Alto Networks - Admin UI**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. Dans la section **Domaine et URL de Palo Alto Networks - Admin UI**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL de Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Customer Firewall FQDN>/php/login.php`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<Customer Firewall FQDN>:443/SAML20/SP`
    
    c. Dans la zone de texte **URL de réponse**, tapez l’URL Assertion Consumer Service (ACS) à l’aide du modèle suivant : `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`
    

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez [l’équipe de support client de Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support) pour obtenir ces valeurs. 
 
4. L’application Palo Alto Networks - Admin UI attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit. Notez que les valeurs des attributs sont seulement des exemples : utilisez donc des valeurs appropriées pour username et adminrole. Il existe un autre attribut facultatif, « accessdomain », qui est utilisé pour restreindre l’accès administrateur à des systèmes virtuels spécifiques sur le pare-feu.
        
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | username | user.userprincipalname |
    | adminrole | customadmin |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

    > [!NOTE]
    > Vous pouvez vous référer aux articles suivants pour plus d’informations sur les attributs.
    > 1. Profil de rôle d’administration pour Admin UI (adminrole) : https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile
    > 2. Domaine d’accès des appareils pour Admin UI (accessdomain) : https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain

6. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

8. Ouvrez l’interface utilisateur de l’administration du pare-feu Palo Alto Networks en tant qu’administrateur dans une autre fenêtre de navigateur.

9. Cliquez sur **Appareil**.

    ![Configurer l’authentification unique Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Sélectionnez **Fournisseur d’identité SAML** dans la barre de navigation gauche et cliquez sur « Importer » pour importer le fichier de métadonnées.

    ![Configurer l’authentification unique Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Effectuez les actions suivantes sur la fenêtre Importer

    ![Configurer l’authentification unique Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Dans la zone de texte **Nom du profil**, spécifiez un nom, par exemple AzureAD Admin UI.
    
    b. Dans **Métadonnées du fournisseur d’identité**, cliquez sur **Parcourir** et sélectionnez le fichier metadata.xml que vous avez téléchargé à partir du portail Azure.
    
    c. Décochez « **Valider le certificat de fournisseur d’identité**».
    
    d. Cliquez sur **OK**
    
    e. Valider les configurations sur le pare-feu en sélectionnant le bouton **Valider**

12. Sélectionnez **Fournisseur d’identité SAML** dans la barre de navigation gauche et cliquez sur le profil du fournisseur d’identité SAML (par exemple, AzureAD Admin UI) créé à l’étape précédente. 
    
  ![Configurer l’authentification unique Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

13. Effectuer les actions suivantes dans la fenêtre **Profil de serveur de fournisseur d’identité SAML**

  ![Configurer le Single Logout Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
  a. Dans la zone de texte **URL SLO du fournisseur d’identité**, supprimez l’URL SLO précédemment importée et ajoutez l’URL suivante : `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
  b. Cliquez sur **OK**


14. Dans Admin UI du pare-feu Palo Alto Networks, cliquez sur **Appareil** et sélectionnez **Rôles d’administrateur**.

15. Cliquez sur le bouton **Add** . Dans la fenêtre Profil de rôle d’administrateur, fournissez un nom pour le rôle d’administrateur (par exemple, fwadmin). Ce nom de rôle d’administrateur doit correspondre au nom de l’attribut Rôle d’administrateur SAML envoyé par le fournisseur d’identité. À l’étape 5, le nom du rôle d’administrateur et sa valeur ont été créés. 

  ![Configurer le rôle d’administrateur Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
16. Dans l’interface Admin UI du pare-feu, cliquez sur **Appareil** et sélectionnez **Profil d’authentification**.

17. Cliquez sur le bouton **Add** . Dans la fenêtre Profil d’authentication, effectuez les actions suivantes : 

 ![Configurer le profil d’authentification Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

   a. Dans la zone de texte **Nom**, indiquez un nom, par exemple AzureSAML_Admin_AuthProfile.
    
   b. Dans la liste déroulante **Type**, sélectionnez **SAML**. 
   
   c. Dans la liste déroulante Profil du serveur IdP, sélectionnez le profil du serveur du fournisseur d’identité SAML approprié (par exemple, AzureAD Admin UI).
   
   c. Cochez la case « **Activer Single Logout** ».
    
   d. Entrez le nom de l’attribut (par exemple, adminrole) dans la zone de texte Attribut du rôle d’administrateur. 
   
   e. Sélectionnez l’onglet Avancé et cliquez sur le bouton **Ajouter** dans le volet de la liste verte. Sélectionnez les utilisateurs et groupes qui peuvent s’authentifier avec ce profil. Lorsqu’un utilisateur s’authentifie, le pare-feu fait correspondre le nom d’utilisateur ou de groupe associé aux entrées figurant dans cette liste. Si vous n’ajoutez pas d’entrées, aucun utilisateur ne peut s’authentifier.
   
   ![Configurer le profil d’authentification Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
   
   f. Cliquez sur **OK**

18. Pour permettre aux administrateurs d’utiliser l’authentification unique SAML à l’aide d’Azure, cliquez sur **Appareil** et sélectionnez **Configuration**. Dans le volet Configuration, sélectionnez l’onglet **Gestion** et cliquez sur l’icône d’engrenage sous **Paramètres d’authentification**. 

 ![Configurer les paramètres d’authentification Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Sélectionnez le profil d’authentification SAML créé à l’étape 17. (Exemple : AzureSAML_Admin_AuthProfile.)

 ![Configurer les paramètres d’authentification Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Cliquez sur **OK**

21. Validez la configuration en sélectionnant le bouton **Valider**.


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Créer un utilisateur de test Palo Alto Networks - Admin UI

Palo Alto Networks - Admin UI prend en charge le provisionnement juste-à-temps des utilisateurs : ainsi, un utilisateur est créé automatiquement dans le système après une authentification réussie s’il n’existe pas encore. Vous ne devez effectuer aucune action ici.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Palo Alto Networks - Admin UI.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Palo Alto Networks - Admin UI, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Palo Alto Networks - Admin UI**.

    ![Lien Palo Alto Networks - Admin UI dans la liste des applications](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Palo Alto Networks - Admin UI dans le panneau d’accès, vous êtes normalement connecté automatiquement à votre application Palo Alto Networks - Admin UI.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

