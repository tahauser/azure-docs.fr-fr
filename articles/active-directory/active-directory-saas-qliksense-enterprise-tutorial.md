---
title: "Didacticiel : Intégration d’Azure Active Directory à Qlik Sense Enterprise | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Qlik Sense Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: cada2ca63e27f4a17f1541679395727ef710c035
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory à Qlik Sense Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer Qlik Sense Enterprise à Azure Active Directory (Azure AD).

L’intégration du logiciel Qlik Sense Enterprise à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Qlik Sense Enterprise.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Qlik Sense Enterprise (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Qlik Sense Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Qlik Sense Enterprise pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Qlik Sense Enterprise à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Ajout de Qlik Sense Enterprise à partir de la galerie
Pour configurer l’intégration de Qlik Sense Enterprise à Azure AD, vous devez ajouter Qlik Sense Enterprise depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Qlik Sense Enterprise à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Qlik Sense Enterprise**, sélectionnez **Qlik Sense Enterprise** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Qlik Sense Enterprise dans la liste des résultats](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Qlik Sense Enterprise avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Qlik Sense Enterprise équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Qlik Sense Enterprise associé doit être établie.

Dans Qlik Sense Enterprise, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Qlik Sense Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user)** pour avoir un équivalent de Britta Simon dans Qlik Sense Enterprise, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Qlik Sense Enterprise.

**Pour configurer l’authentification unique Azure AD avec Qlik Sense Enterprise, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Qlik Sense Enterprise**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Dans la section **Domaine et URL Qlik Sense Enterprise**, procédez comme suit :

    ![Informations d’authentification unique de domaine et d’URL Qlik Sense Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Notez la barre oblique à la fin de cet URI. Elle est obligatoire.
    
    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL d’authentification et l’identificateur réels qui sont décrits plus loin dans le didacticiel, ou contactez l’[équipe de support technique Qlik Sense Enterprise](https://www.qlik.com/us/services/support) pour obtenir ces valeurs. 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Préparez le fichier XML de métadonnées de fédération afin de le charger vers le serveur Qlik Sense.
   
    > [!NOTE]
    > Avant de charger les métadonnées IdP vers le serveur Qlik Sense, vous devez modifier le fichier et supprimer des informations pour vous assurer du bon fonctionnement entre Azure AD et le serveur Qlik Sense.
    
    ![QlikSense][qs24]
   
    a. Ouvrez le fichier FederationMetaData.xml téléchargé à partir du portail Azure dans un éditeur de texte.
   
    b. Recherchez la valeur **RoleDescriptor**.  Il en existe quatre entrées (deux paires de balises d’élément d’ouverture et de fermeture).
   
    c. Dans le fichier, supprimez les balises RoleDescriptor et toutes les informations insérées entre celles-ci.
   
    d. Enregistrez le fichier et conservez-le pour l’utiliser ultérieurement dans ce document.

7. Accédez à la console de gestion Qlik (QCM) de Qlik Sense en tant qu’utilisateur pouvant créer des configurations de proxy virtuel.

8. Dans la console QMC, cliquez sur l’élément de menu **Proxys virtuels**.
   
    ![QlikSense][qs6] 

9. En bas de l’écran, cliquez sur le bouton **Créer nouveau**.
   
    ![QlikSense][qs7]

10. L’écran Virtual proxy edit (Modification du proxy virtuel) s’affiche.  Un menu permettant de rendre les options de configuration visibles est affiché sur le côté droit de l’écran.
   
    ![QlikSense][qs9]

11. L’option de menu Identification étant sélectionnée, entrez les informations d’identification pour la configuration du proxy virtuel Azure.
    
    ![QlikSense][qs8]  
    
    a. Le champ **Description** contient un nom convivial pour la configuration du proxy virtuel.  Entrez une valeur pour la description.
    
    b. Le champ **Préfixe** identifie le point de terminaison proxy virtuel pour la connexion à Qlik Sense avec l’authentification unique Azure AD.  Entrez un nom de préfixe unique pour ce proxy virtuel.
    
    c. La valeur **Session inactivity timeout** (Délai d’inactivité de session) (minutes) représente le délai d’expiration des connexions via ce proxy virtuel.
    
    d. La zone **Session cookie header name** (Nom d’en-tête de cookie de session) est le nom du cookie stockant l’identificateur de session pour la session Qlik Sense qu’un utilisateur reçoit après une authentification réussie.  Ce nom doit être unique.

12. Cliquez sur l’option de menu Authentification pour la rendre visible.  L’écran Authentification s’affiche.
    
    ![QlikSense][qs10]
    
    a. La liste déroulante **Anonymous access mode (Mode d’accès anonyme)** détermine si des utilisateurs anonymes peuvent accéder à Qlik Sense via le proxy virtuel.  L’option par défaut est No anonymous user (Pas d’utilisateur anonyme).
    
    b. La liste déroulante **Méthode d’authentification** détermine le schéma d’authentification qui sera utilisé par le proxy virtuel.  Dans la liste déroulante, sélectionnez SAML.  À la suite de cette sélection, d’autres options s’affichent.
    
    c. Dans le champ **SAML host URI**(URI d’hôte SAML), indiquez le nom d’hôte que les utilisateurs doivent entrer pour accéder à Qlik Sense via ce proxy virtuel SAML.  Le nom d’hôte est l’URI du serveur Qlik Sense.
    
    d. Dans le champ **SAML entity ID (ID d’entité SAML)**, entrez la valeur indiquée dans le champ SAML host URI (URI d’hôte SAML).
    
    e. La zone **SAML IdP metadata** (Métadonnées IdP SAML) indique le fichier modifié précédemment dans la section **Modifier les métadonnées de fédération dans la configuration d’Azure AD**.  **Avant de charger les métadonnées IdP, vous devez modifier le fichier** et supprimer des informations pour vous assurer du bon fonctionnement entre Azure AD et le serveur Qlik Sense.  **Reportez-vous aux instructions ci-dessus si le fichier doit encore être modifié.**  Si le fichier a été modifié, cliquez sur le bouton Parcourir et sélectionnez le fichier de métadonnées modifié pour le charger vers la configuration du proxy virtuel.
    
    f. Entrez le nom d’attribut ou la référence de schéma pour l’attribut SAML représentant **l’ID utilisateur** qu’Azure AD envoie au serveur Qlik Sense.  Les informations de référence de schéma sont disponibles dans les écrans de l’application Azure post-configuration.  Pour utiliser le nom d’attribut, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Entrez la valeur du **répertoire utilisateur** qui est attaché aux utilisateurs lorsqu’ils s’authentifient sur le serveur Qlik Sense via Azure AD.  Les valeurs codées en dur doivent être entourées de **crochets []**.  Pour utiliser un attribut envoyé dans l’assertion SAML Azure AD, entrez son nom dans cette zone de texte **sans** crochets.
    
    h. **L’algorithme de signature SAML** définit la signature du certificat du fournisseur de service (dans ce cas le serveur Qlik Sense) pour la configuration du proxy virtuel.  Si le serveur Qlik Sense utilise un certificat approuvé généré à l’aide de Microsoft Enhanced RSA and AES Cryptographic Provider, définissez l’algorithme de signature SAML sur **SHA-256**.
    
    i. La section SAML attribute mapping (Mappage d’attributs SAML) permet d’envoyer des attributs supplémentaires, comme des groupes, vers Qlik Sense pour les utiliser dans les règles de sécurité.

13. Cliquez sur l’option de menu **ÉQUILIBRAGE DE CHARGE** pour la rendre visible.  L’écran Équilibrage de charge s’affiche.
    
    ![QlikSense][qs11]

14. Cliquez sur le bouton **Add new server node** (Ajouter un nouveau nœud serveur), sélectionnez un ou plusieurs nœuds de moteur auxquels Qlik Sense enverra des sessions à des fins d’équilibrage de charge, puis cliquez sur le bouton **Ajouter**.
    
    ![QlikSense][qs12]

15. Cliquez sur l’option de menu Avancé pour la rendre visible. L’écran Avancé s’affiche.
    
    ![QlikSense][qs13]
    
    La liste des hôtes approuvés identifie les noms d’hôte qui sont acceptés lors de la connexion au serveur Qlik Sense.  **Entrez le nom d’hôte spécifié par les utilisateurs pour se connecter au serveur Qlik Sense.** Le nom d’hôte est défini sur la même valeur que l’URI d’hôte SAML sans https://.

16. Cliquez sur le bouton **Appliquer**.
    
    ![QlikSense][qs14]

17. Cliquez sur OK pour accepter le message d’avertissement indiquant que les proxys liés au proxy virtuel vont être redémarrés.
    
    ![QlikSense][qs15]

18. Sur le côté droit de l’écran, le menu Éléments associés s’affiche.  Cliquez sur l’option de menu **Proxys**.
    
    ![QlikSense][qs16]

19. L’écran Proxy s’affiche.  Cliquez sur le bouton **Lier** en bas de l’écran pour lier un proxy au proxy virtuel.
    
    ![QlikSense][qs17]

20. Sélectionnez le nœud proxy qui prendra en charge cette connexion de proxy virtuel et cliquez sur le bouton **Lier**.  Après l’établissement du lien, le proxy est répertorié dans les proxys associés.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Après environ cinq à dix secondes, le message Refresh QMC (Actualiser la console QMC) s’affiche.  Cliquez sur le bouton **Refresh QMC** (Actualiser la console QMC).
    
    ![QlikSense][qs20]

22. Lors de l’actualisation de la console QMC, cliquez sur l’élément de menu **Proxys virtuels**. La nouvelle entrée de proxy virtuel SAML est indiquée dans le tableau affiché à l’écran.  Cliquez sur l’entrée de proxy virtuel.
    
    ![QlikSense][qs51]

23. En bas de l’écran, le bouton Download SP metadata (Télécharger les métadonnées du fournisseur de service) est activé.  Cliquez sur le bouton **Download SP metadata** (Télécharger les métadonnées du fournisseur de service) pour enregistrer les métadonnées dans un fichier.
    
    ![QlikSense][qs52]

24. Ouvrez le fichier de métadonnées du fournisseur de service.  Observez les entrées **entityID** et **AssertionConsumerService**.  Ces valeurs sont équivalentes à celles des champs **Identificateur** et **URL de connexion** dans la configuration de l’application Azure AD. Collez ces valeurs dans la section **Domaine et URL Qlik Sense Enterprise** de la configuration de l’application Azure AD si elles ne sont pas identiques. Vous devez ensuite les remplacer dans l’assistant de configuration de l’application Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

   ![Bouton Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

   ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

   ![Bouton Ajouter](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

   ![Boîte de dialogue Utilisateur](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. Dans la zone **Nom**, tapez **BrittaSimon**.

   b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

   c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

   d. Cliquez sur **Create**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Créer un utilisateur de test Qlik Sense Enterprise

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Qlik Sense Enterprise. Collaborez avec l’[équipe du support technique Qlik Sense Enterprise](https://www.qlik.com/us/services/support) pour ajouter les utilisateurs à la plateforme Qlik Sense Enterprise. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Qlik Sense Enterprise.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Qlik Sense Enterprise, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Qlik Sense Enterprise**.

    ![Dans la liste des applications, sélectionnez Qlik Sense Enterprise.](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Qlik Sense Enterprise dans le volet d’accès, vous devez être connecté automatiquement à votre application Qlik Sense Enterprise. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

