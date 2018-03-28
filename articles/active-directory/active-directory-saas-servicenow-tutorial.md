---
title: 'Didacticiel : Intégration d’Azure Active Directory à ServiceNow | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: jeedes
ms.openlocfilehash: d893b55e2e771035bbd1097da678830fafb24e7a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Didacticiel : Intégration d’Azure Active Directory à ServiceNow

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) dans ServiceNow.

L’intégration de ServiceNow dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ServiceNow.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ServiceNow (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à ServiceNow, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Pour ServiceNow, une instance ou un locataire ServiceNow, version Calgary ou supérieure
- Pour ServiceNow Express, une instance ServiceNow Express, version Helsinki ou supérieure
- Le locataire ServiceNow doit avoir le [plug-in d’authentification unique à plusieurs fournisseurs](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) activé. Cette opération est possible en [envoyant une demande de service](https://hi.service-now.com).
- Pour la configuration automatique, activez le plug-in multifournisseur pour ServiceNow.

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ServiceNow à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-servicenow-from-the-gallery"></a>Ajout de ServiceNow à partir de la galerie
Pour configurer l’intégration de ServiceNow à Azure AD, vous devez ajouter ServiceNow depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ServiceNow à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **ServiceNow**, sélectionnez **ServiceNow** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![ServiceNow dans la liste des résultats](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de ServiceNow avec un utilisateur de test appelé «  Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ServiceNow équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur ServiceNow associé doit être établie.

Dans ServiceNow, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ServiceNow, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD pour ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Azure AD pour ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Créer un utilisateur de test ServiceNow](#create-a-servicenow-test-user)** pour avoir un équivalent de Britta Simon dans ServiceNow lié à la représentation d’utilisateur dans Azure AD.
5. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurer l’authentification unique Azure AD pour ServiceNow

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ServiceNow.

**Pour configurer l’authentification unique Azure AD auprès de ServiceNow, effectuez les étapes suivante :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **ServiceNow**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Dans la section **Domaine et URL ServiceNow**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL ServiceNow](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<instance-name>.service-now.com/navpage.do`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous devez changer cette valeur pour l’URL de connexion et l’identificateur réels. Ceci est expliqué plus loin dans le didacticiel.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Pour générer l’URL des **métadonnées**, effectuez les étapes suivantes :

    a. Cliquez sur **Inscriptions des applications**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/appregistrations.png)

    b. Cliquez sur **Points de terminaison** pour ouvrir la boîte de dialogue **Points de terminaison**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/endpointicon.png)
    
    c. Cliquez sur le bouton Copier pour copier l’URL du document de métadonnées de fédération (**FEDERATION METADATA DOCUMENT**), puis collez-la dans le Bloc-notes.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/endpoint.png)

    d. Accédez maintenant aux propriétés de **ServiceNow**, puis copiez l’**ID d’application** à l’aide du bouton **Copier** et collez-le dans le Bloc-notes.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/appid.png)

    e. Générez l’**URL des métadonnées** en utilisant le modèle suivant : `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.  Copiez la valeur générée dans le Bloc-notes car cette URL des métadonnées sera utilisée ultérieurement dans le didacticiel.

7. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

8. Activez le plug-in **Integration - Multiple Provider Single Sign-On Installer** (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs) en suivant la procédure ci-dessous :

    a. Dans le volet de navigation sur le côté gauche, recherchez la section **System Definition** (Définition du système) à partir de la barre de recherche, puis cliquez sur **Plug-ins**.

    ![Activer le plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Activer le plug-in")

     b. Recherchez **Integration - Multiple Provider Single Sign-On Installer** (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs).

     ![Activer le plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Activer le plug-in")

    c. Sélectionnez le plug-in. Cliquez avec le bouton droit et sélectionnez **Activate/Upgrade** (Activer/Mettre à niveau).

    d. Cliquez sur le bouton **Activate** (Activer).

9. Il existe deux modes de configuration de **ServiceNow** : automatique et manuel.

10. Pour configurer **ServiceNow** automatiquement, suivez les étapes ci-dessous :

    a. Revenez à la page d’authentification unique **ServiceNow** sur le Portail Azure.

    b. Un service de configuration en un seul clic est fourni pour ServiceNow, autrement dit, pour qu’Azure AD configure automatiquement ServiceNow pour l’authentification basée sur SAML. Pour activer ce service, accédez à la section **Configuration de ServiceNow**, cliquez sur **Configurer ServiceNow** pour ouvrir la fenêtre Configurer l’authentification.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Entrez votre nom d’instance ServiceNow, votre nom d’utilisateur administrateur et votre mot de passe d’administrateur dans le formulaire **Configurer l’authentification** et cliquez sur **Configurer maintenant**. Notez que le nom d’utilisateur administrateur fourni doit avoir le rôle **security_admin** attribué dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow pour utiliser Azure AD comme fournisseur d’identité SAML, cliquez sur **Configurer manuellement l’authentification unique** et copiez l’**URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la section Référence rapide.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/configure.png "Configurer l’URL de l’application")

    d. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

    e. Dans la configuration automatique, tous les paramètres nécessaires sont configurés du côté de **ServiceNow**, mais le **Certificat X.509** n’est pas activé par défaut. Vous devrez le mapper manuellement à votre fournisseur d’identité dans ServiceNow. Suivez les étapes ci-dessous :
    
    * Dans le volet de navigation sur le côté gauche, cliquez sur **Fournisseurs d’identité** sous **SSO multifournisseur**.

      ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

    * Cliquez sur le fournisseur d’identité généré automatiquement.

      ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_08.png "Configurer l’authentification unique")

    * Faites défiler jusqu’à la section **Certificat X.509**. Sélectionnez **Modifier**.

      ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_09.png "Configurer l’authentification unique")
    
    * Sélectionnez le certificat et cliquez sur l’icône de flèche droite pour ajouter le certificat.

      ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_11.png "Configurer l’authentification unique")

    * Cliquez sur **Enregistrer**.

    * Cliquez sur **Activer** en haut à droite de la page.

11. Pour configurer **ServiceNow** manuellement, suivez les étapes ci-dessous :

12. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

13. Dans le volet de navigation sur le côté gauche, recherchez la section **Multi-Provider SSO** (SSO multifournisseur) à partir de la barre de recherche, puis cliquez sur **Propriétés**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurer l’URL de l’application")

14. Dans la boîte de dialogue **Multiple Provider SSO Properties** , effectuez les opérations suivantes :

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "Configurer l’URL de l’application")

    a. Pour **Enable multiple provider SSO**, sélectionnez **Yes**.

    b. Pour **Enable Auto Importing of users from all identity providers into the user table** (Activer l’importation automatique des utilisateurs à partir de tous les fournisseurs d’identité dans la table utilisateur), sélectionnez **Yes** (Oui).

    c. Pour **Enable debug logging got the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique auprès de plusieurs fournisseurs), sélectionnez **Yes** (Oui).

    d. Dans la zone de texte **The field on the user table that...**, entrez **user_name**.

    e. Cliquez sur **Enregistrer**.

14. Dans le volet de navigation sur le côté gauche, recherchez la section **Multi-Provider SSO** (SSO multifournisseur) à partir de la barre de recherche, puis cliquez sur **x509 Certificates** (Certificats x509).

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurer l’authentification unique")

15. Dans la boîte de dialogue **X.509 Certificates**, cliquez sur **New**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "Configurer l’authentification unique")

16. Dans la boîte de dialogue **X.509 Certificates** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Name**, indiquez le nom de votre configuration (par exemple, **TestSAML2.0**).

    b. Sélectionnez **Active**.

    c. Pour **Format**, sélectionnez **PEM**.

    d. Pour **Type**, sélectionnez **Trust Store Cert**.

    e. Ouvrez, dans le Bloc-notes, votre certificat codé en base 64 téléchargé à partir d’Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat PEM**.

     f. Cliquez sur **Envoyer**.

17. À gauche du volet de navigation, cliquez sur **Identity Providers**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

18. Dans la boîte de dialogue **Fournisseurs d’identité**, cliquez sur **Nouveau**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "Configurer l’authentification unique")

19. Dans la boîte de dialogue **Fournisseurs d’identité**, cliquez sur **SAML2 Update1?**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "Configurer l’authentification unique")

20. Dans la boîte de dialogue SAML2 Update1 Properties, effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/idp.png "Configurer l’authentification unique")

    a. Sélectionnez l’option **URL** dans la boîte de dialogue **Import Identity Provider Metadata** (Importer les métadonnées de fournisseur d’identité).

    b. Entrez l’**URL des métadonnées** générée à partir du portail Azure.

    c. Cliquez sur **Importer**.

21. L’URL des métadonnées IdP est alors lue et tous les champs sont renseignés.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Name**, indiquez le nom de votre configuration (par exemple, **SAML 2.0**).
    
    b. Copiez la valeur **ServiceNow Homepage**, collez-la dans la zone de texte **URL de connexion** dans la section **Domaine et URL ServiceNow** dans le portail Azure.

    > [!NOTE]
    > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

    c. Copiez la valeur **Id d’entité/Émetteur**, collez-la dans la zone de texte **Identificateur** dans la section **Domaine et URL ServiceNow** du portail Azure.

    d. Cliquez sur **Avancé**. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.

    > [!NOTE]
    > Vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attributs > Authentification unique) du portail Azure et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ renseigné (par exemple, user_name).

     e. Pour **Certificat x509**, indiquez le certificat que vous avez créé à l’étape précédente.

     > [!NOTE]
     > ServiceNow n’autorise pas l’activation de l’IdP si vous n’avez pas cliqué sur le bouton Tester la connexion ; pour effectuer le remplacement, suivez les étapes ci-dessous.

22. Cliquez sur l’icône de menu du fournisseur d’identité que vous venez de créer dans le cadre de la configuration ; sélectionnez **Copier sys_id** dans la liste.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694992.png "Configurer l’authentification unique")

23. Dans la zone de recherche en haut à gauche, recherchez **sys_properties.list** et appuyez sur ENTRÉE.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694993.png "Configurer l’authentification unique")

24. Cliquez sur **Nouveau**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "Configurer l’authentification unique")

25. Dans la section **Propriété système**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694995.png "Configurer l’authentification unique")

    a. Entrez la valeur `glide.authenticate.sso.redirect.idp` dans la zone de texte du nom.

    b. Dans la zone de texte **Valeur**, collez la valeur sys_id que vous avez copiée aux étapes précédentes.

    c. Sélectionnez **Privé**.

    d. Cliquez sur **Envoyer**.

26. Cliquez sur **Nouveau**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "Configurer l’authentification unique")

27. Dans la section **Propriété système**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694996.png "Configurer l’authentification unique")

    a. Entrez la valeur `glide.authenticate.multisso.test.connection.mandatory` dans la zone de texte du nom.

    b. Dans la zone de texte **Valeur**, entrez **False**.

    c. Cliquez sur **Envoyer**.

28. Vous pouvez maintenant activer votre nouveau fournisseur d’identité ; l’authentification unique devrait fonctionner.

> [!NOTE]
> Notez également que vous devez tester votre nouvelle configuration IdP dans une nouvelle fenêtre de navigation privée.

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configurer l’authentification unique Azure AD pour ServiceNow Express

1. Dans le Portail Azure, dans la page d’intégration de l’application **ServiceNow**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Dans la section **Domaine et URL ServiceNow**, effectuez les étapes suivantes :

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://<instance-name>.service-now.com/navpage.do`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support ServiceNow](https://www.servicenow.com/support/contact-support.html).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Un service de configuration en un seul clic est fourni pour ServiceNow, autrement dit, pour qu’Azure AD configure automatiquement ServiceNow pour l’authentification basée sur SAML. Pour activer ce service, accédez à la section **Configuration de ServiceNow**, cliquez sur **Configurer ServiceNow** pour ouvrir la fenêtre Configurer l’authentification.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. Entrez votre nom d’instance ServiceNow, votre nom d’utilisateur administrateur et votre mot de passe d’administrateur dans le formulaire **Configurer l’authentification** et cliquez sur **Configurer maintenant**. Notez que le nom d’utilisateur administrateur fourni doit avoir le rôle **security_admin** attribué dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow pour utiliser Azure AD comme fournisseur d’identité SAML, cliquez sur **Configurer manuellement l’authentification unique** et copiez l’**URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la section Référence rapide.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/configure.png "Configurer l’URL de l’application")

8. Connectez-vous à votre application ServiceNow Express en tant qu’administrateur.

9. Dans le volet de navigation à gauche, cliquez sur **Authentification unique**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurer l’URL de l’application")

10. Dans la boîte de dialogue **Authentification unique**, cliquez sur l’icône de configuration en haut à droite et définissez les propriétés suivantes :

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurer l’URL de l’application")

    a. Activez **Enable multiple provider SSO** (Activer l’authentification unique à plusieurs fournisseurs) à droite.
    
    b. Activez **Enable debug logging for the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique à plusieurs fournisseurs) à droite.
    
    c. Dans la zone de texte **The field on the user table that...**, entrez **user_name**.

11. Dans la boîte de dialogue **Authentification unique**, cliquez sur **Add New Certificate** (Ajouter un nouveau certificat).

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurer l’authentification unique")

12. Dans la boîte de dialogue **X.509 Certificates** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Name**, indiquez le nom de votre configuration (par exemple, **TestSAML2.0**).

    b. Sélectionnez **Active**.

    c. Pour **Format**, sélectionnez **PEM**.

    d. Pour **Type**, sélectionnez **Trust Store Cert**.

    e. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat PEM** .

    f. Cliquez sur **Mettre à jour**

13. Dans la boîte de dialogue **Authentification unique**, cliquez sur **Add New IdP** (Ajouter un nouveau fournisseur d’identité).

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurer l’authentification unique")

14. Dans la boîte de dialogue **Add New Identity Provider** (Ajouter un nouveau fournisseur d’identité), sous **Configure Identity Provider** (Configurer un fournisseur d’identité), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Name**, indiquez le nom de votre configuration (par exemple, **SAML 2.0**).

    b. Dans le champ **URL du fournisseur d’identité**, collez la valeur **ID du fournisseur d’identité** copiée sur le Portail Azure.
    
    c. Dans le champ **AuthnRequest du fournisseur d’identité**, collez la valeur **URL de la demande d’authentification** copiée sur le Portail Azure.

    d. Dans le champ **SingleLogoutRequest du fournisseur d’identité**, collez la valeur **URL du service de déconnexion unique** copiée sur le Portail Azure.

    e. Pour **Identity Provider Certificate** (Certificat du fournisseur d’identité), sélectionnez le certificat que vous avez créé à l’étape précédente.

15. Cliquez sur **Advanced Settings** (Paramètres avancés), et sous **Additional Identity Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Liaison du protocole pour la demande de déconnexion unique du fournisseur d’identité**, entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Dans la zone de texte **Stratégie d’ID de nom**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. Dans **AuthnContextClassRef Method**, entrez `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Désélectionnez **Créer une classe de contexte d’authentification**.

16. Sous **Additional Service Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Page d’accueil ServiceNow** , entrez l’URL de la page d’accueil de votre instance ServiceNow.

    > [!NOTE]
    > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

    b. Dans la zone de texte **ID de l’entité / Émetteur** , entrez l’URL de votre locataire ServiceNow.

    c. Dans la zone de texte **URI d’audience** , entrez l’URL de votre locataire ServiceNow.

    d. Dans la zone de texte **Variation d’horloge**, entrez **60**.

    e. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.

    > [!NOTE]
    > Vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attributs > Authentification unique) du portail Azure et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ renseigné (par exemple, user_name).

    f. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-servicenow-test-user"></a>Créer un utilisateur de test ServiceNow

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans ServiceNow. Si vous ne savez pas comment ajouter un utilisateur dans votre compte ServiceNow ou ServiceNow Express, contactez l’[équipe de support ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ServiceNow.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à ServiceNow, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ServiceNow**.

    ![Lien ServiceNow dans la liste des applications](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette ServiceNow dans le volet d’accès, vous devez être connecté automatiquement à votre application ServiceNow.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

