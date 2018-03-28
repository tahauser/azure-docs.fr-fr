---
title: 'Didacticiel : Intégrer Azure Active Directory dans Palo Alto Networks - Admin UI | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks - Admin UI.
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
ms.openlocfilehash: c5be53f06e009cb2d5180e43318c8670139a68db
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Intégrer Azure Active Directory dans Palo Alto Networks - Admin UI

Dans ce didacticiel, vous découvrez comment intégrer Azure Active Directory (Azure AD) dans Palo Alto Networks - Admin UI.

L’intégration d’Azure AD dans Palo Alto Networks - Admin UI vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks - Admin UI.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Palo Alto Networks - Admin UI (via l’authentification unique ou SSO) avec leur compte Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS dans Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD avec Palo Alto Networks - Admin UI, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un pare-feu Palo Alto Networks de nouvelle génération ou un dispositif Panorama (système de gestion centralisée pour les pare-feu)

> [!NOTE]
> Lorsque vous testez les étapes de ce didacticiel, nous *déconseillons* l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de Palo Alto Networks - Admin UI depuis la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Ajouter Palo Alto Networks - Admin UI depuis la galerie
Pour configurer l’intégration d’Azure AD dans Palo Alto Networks - Admin UI, ajoutez oPalo Alto Networks - Admin UI à votre liste d’applications SaaS gérées à partir de la galerie en procédant comme suit :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Fenêtre « Applications d’entreprise »][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la fenêtre.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Palo Alto Networks - Admin UI**, sélectionnez **Palo Alto Networks - Admin UI** dans la liste des résultats, puis sélectionnez **Ajouter**.

    ![Palo Alto Networks - Admin UI dans la liste des résultats](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Palo Alto Networks - Admin UI sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit identifier l’utilisateur Palo Alto Networks - Admin UI et son équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et le même utilisateur dans Palo Alto Networks - Admin UI.

Pour établir la relation, assignez comme *Nom d’utilisateur* Palo Alto Networks - Admin UI la valeur du *nom d’utilisateur* dans Azure AD.

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks - Admin UI, suivez les indications des sections dans les cinq sections suivantes.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Palo Alto Networks - Admin UI de la manière suivante :

1. Dans le portail Azure, à la page d’intégration de l’application **Palo Alto Networks - Admin UI**, sélectionnez **Authentification unique**.

    ![Lien « Authentification unique »][4]

2. Dans la zone **Mode d’authentification unique** de la fenêtre **Authentification unique**, sélectionnez **Authentification SAML**.
 
    ![Fenêtre « Authentification unique »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. Sous **Domaine et URL de Palo Alto Networks - Admin UI**, procédez comme suit :

    ![Informations d’authentification unique dans « Domaine et URL de Palo Alto Networks - Admin UI »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Dans la zone **URL d’authentification**, entrez une URL au format suivant : *https://\<Customer Firewall FQDN>/php/login.php*.

    b. Dans la zone **Identificateur**, entrez une URL au format suivant : *https://\<Customer Firewall FQDN>:443/SAML20/SP*.
    
    c. Dans la zone **URL de réponse**, tapez l’URL du Service ACS (Assertion Consumer) au format suivant : *https://\<Customer Firewall FQDN>: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas réelles. Mettez-les à jour avec l’URL de connexion et l’identificateur réels. Pour obtenir les valeurs, contactez [l’équipe de support client de Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). 
 
4. Étant donné que l’application Palo Alto Networks - Admin UI attend les assertions SAML dans un format spécifique, configurez les revendications conformément à l’illustration suivante. Gérez les valeurs d’attributs dans la section **Attributs utilisateur** de la page **Intégration des applications** en procédant comme suit :
    
    ![La liste des attributs du jeton SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Étant donné que les valeurs d’attributs ne sont que des exemples, mappez les valeurs appropriées pour *nom d’utilisateur* et *adminrole*. Il existe un autre attribut facultatif, *accessdomain*, qui est utilisé pour restreindre l’accès administrateur à des systèmes virtuels spécifiques sur le pare-feu.
   >
        
    | Nom de l’attribut | Valeur de l’attribut |
    | --- | --- |    
    | username | user.userprincipalname |
    | adminrole | customadmin |

    a. Sélectionnez **Ajouter un attribut**.  
    
    ![Le bouton « Ajouter un attribut »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    La fenêtre **Ajouter un attribut** s’affiche.

    ![La fenêtre « Ajouter un attribut »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la zone **Valeur**, tapez la valeur de l’attribut correspondant à cette ligne.
    
    d. Sélectionnez **OK**.

    > [!NOTE]
    > Pour plus d'informations sur les attributs, consultez les articles suivants :
    > * [Profil de rôle d’administrateur pour Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domaine d’accès de périphérique pour Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. Sous **Certificat de signature SAML**, sélectionnez **XML des métadonnées**, puis **Enregistrer**.

    ![Lien de téléchargement du fichier XML des métadonnées](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Bouton Enregistrer](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

6. Ouvrez l’interface utilisateur de l’administration du pare-feu Palo Alto Networks en tant qu’administrateur dans une nouvelle fenêtre.

7. Sélectionnez l’onglet **Appareil**.

    ![L’onglet de l’appareil](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Dans le volet gauche, sélectionnez **Fournisseur d’identité SAML**, puis sélectionnez **Importer** pour importer le fichier de métadonnées.

    ![Le bouton Importer un fichier de métadonnées](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Dans la fenêtre **Importer le profil du serveur du fournisseur d’identité SAML**, procédez comme suit :

    ![La fenêtre « Importer le profil du serveur du fournisseur d’identité SAML »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Dans la zone **Nom du profil**, spécifiez un nom (par exemple **AzureAD Admin UI**).
    
    b. Cliquez sur **Nouveau fournisseur d’identité**, sélectionnez **Parcourir** et le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.
    
    c. Décochez **Valider le certificat de fournisseur d’identité**.
    
    d. Sélectionnez **OK**.
    
    e. Pour valider les configurations sur le pare-feu, sélectionnez **Valider**.

10. Dans le volet gauche, sélectionnez **Fournisseur d’identité SAML**, puis le profil de fournisseur d’identité SAML (par exemple, **AzureAD Admin UI**) créé à l’étape précédente. 

    ![Le profil du fournisseur d’identité SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. Dans la fenêtre **Profil du serveur du fournisseur d’identité SAML**, procédez comme suit :

    ![La fenêtre « Profil du serveur du fournisseur d’identité SAML »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Dans la zone **URL SLO du fournisseur d’identité**, remplacez l’URL SLO précédemment importée par l’URL suivante : **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Sélectionnez **OK**.

12. Dans Admin UI du pare-feu Palo Alto Networks, sélectionnez **Appareil**, puis **Rôles d’administrateur**.

13. Sélectionnez le bouton **Ajouter**. 

14. Dans la fenêtre **Profil de rôle d’administrateur**, dans la zone **Nom**, indiquez un nom pour le rôle d’administrateur (par exemple, **fwadmin**).  
    Le nom du rôle d’administrateur doit correspondre au nom de l’attribut Rôle d’administrateur SAML envoyé par le fournisseur d’identité. Le nom du rôle d’administrateur et sa valeur ont été créés à l’étape 4.

    ![Configurer le rôle d’administrateur Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Dans l’interface Admin UI du pare-feu, sélectionnez **Appareil**, puis **Profil d’authentification**.

16. Sélectionnez le bouton **Ajouter**. 

17. Dans la fenêtre **Profil d’authentication**, procédez comme suit : 

    ![La fenêtre « Profil d’authentification »](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Dans la zone **Nom**, indiquez un nom (par exemple, **AzureSAML_Admin_AuthProfile**).
    
    b. Dans la liste déroulante **Type**, sélectionnez **SAML**. 
   
    c. Dans la liste déroulante **Profil du serveur IdP**, sélectionnez le profil du serveur du fournisseur d’identité SAML approprié (par exemple, **AzureAD Admin UI**).
   
    c. Cochez la case **Activer Single Logout**.
    
    d. Dans la zone **Attribut du rôle administrateur**, entrez le nom d’attribut (par exemple, **adminrole**). 
    
    e. Sélectionnez l’onglet **Avancé**, puis, sous **Liste verte**, sélectionnez **Ajouter**. 
    
    ![Le bouton Ajouter dans l’onglet Avancé](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Cochez la case **Tous**, ou sélectionnez les utilisateurs et groupes qui peuvent s’authentifier avec ce profil.  
    Lorsqu’un utilisateur s’authentifie, le pare-feu fait correspondre le nom d’utilisateur ou de groupe associé aux entrées figurant dans cette liste. Si vous n’ajoutez pas d’entrées, aucun utilisateur ne peut s’authentifier.

    g. Sélectionnez **OK**.

18. Pour permettre aux administrateurs d’utiliser SAML SSO à l’aide d’Azure, sélectionnez **Appareil** > **Installation**. Dans le volet **Installation**, sélectionnez l’onglet **Gestion** puis, sous **Paramètres d’authentification**, sélectionnez le bouton **Paramètres** (« engrenage »). 

 ![Le bouton Paramètres](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Sélectionnez le profil de l’authentification SAML créé à l’étape 17 (par exemple, **AzureSAML_Admin_AuthProfile**).

 ![Le champ Profil d’authentification](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Sélectionnez **OK**.

21. Pour valider la configuration, sélectionnez **Valider**.


> [!TIP]
> Au moment de configurer l’application, vous pouvez lire une version abrégée des instructions précédentes sur le [Portail Azure](https://portal.azure.com). Après avoir ajouté l’application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**, puis accédez à la documentation intégrée dans la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez la [documentation incorporée d’Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure en procédant comme suit :

![Créer un utilisateur de test Azure AD][100]

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Lien Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Pour afficher une liste des utilisateurs actuels, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. En haut de la fenêtre **Tous les utilisateurs**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    La fenêtre **Utilisateur** s’ouvre.

4. Dans la fenêtre **Utilisateur**, suivez les étapes ci-dessous :

    ![Fenêtre Utilisateur](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Créer un utilisateur de test Palo Alto Networks - Admin UI

Palo Alto réseaux - Admin UI prend en charge l’approvisionnement de l’utilisateur juste-à-temps. Si un utilisateur n’existe pas encore, il est automatiquement créé dans le système après une authentification réussie. Aucune action n’est nécessaire pour créer l’utilisateur.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez l’utilisatrice Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Palo Alto Networks - Admin UI. Pour ce faire, procédez comme suit :

![Attribuer le rôle utilisateur][200] 

1. Sur le Portail Azure, ouvrez la vue **Applications**, accédez à la vue **Répertoire**, puis sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Liens « Applications d’entreprise » et « Toutes les applications »][201] 

2. Dans la liste **Applications**, sélectionnez **Palo Alto Networks - Admin UI**.

    ![Le lien Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez **Ajouter** puis, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la liste **Utilisateurs** de la fenêtre **Utilisateurs et groupes**, sélectionnez **Britta Simon**.

6. Cliquez sur le bouton **Sélectionner**.

7. Dans la fenêtre **Ajouter une affectation**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la mosaïque Palo Alto Networks - Admin UI dans le panneau d’accès, vous êtes normalement connecté automatiquement à votre application Palo Alto Networks - Admin UI.

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

