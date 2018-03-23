---
title: 'Didacticiel : Intégration d’Azure Active Directory à SCC LifeCycle | Microsoft Docs'
description: Découvrez comment utiliser SCC LifeCycle avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Didacticiel : Intégration d’Azure AD à SCC LifeCycle
L’objectif de ce didacticiel est de montrer comment intégrer Azure et SCC LifeCycle.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement SCC LifeCycle pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SCC LifeCycle pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise SCC LifeCycle (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour SCC LifeCycle
2. Configuration de l'authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scénario")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Activer l’intégration d’applications pour SCC LifeCycle
Cette section décrit comment activer l’intégration d’applications pour SCC LifeCycle.

**Pour activer l’intégration d’applications pour SCC LifeCycle, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **SCC LifeCycle**.
   
    ![Galerie d’applications](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **SCC LifeCycle**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur SCC LifeCycle avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans la page d’intégration d’application **SCC LifeCycle** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SCC LifeCycle**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurer l’authentification unique")
3. Dans la zone de texte **URL de connexion** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SCC LifeCycle à l’aide du modèle suivant « *https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx* », puis cliquez sur **Suivant**.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur SCC LifeCycle**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurer l’authentification unique")
5. Transférez ce fichier de métadonnées à l’équipe de support de SCC LifeCycle.
   
   >[!NOTE]
   >L’authentification unique doit être activée par l’équipe de support de SCC LifeCycle.
   > 
   > 

6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour se connecter à SCC LifeCycle, les utilisateurs d’Azure AD doivent être approvisionnés dans SCC LifeCycle. Vous n’avez rien à faire pour configurer l’approvisionnement des utilisateurs dans SCC LifeCycle.

Lorsqu’un utilisateur tente de se connecter à SCC LifeCycle, un compte SCC LifeCycle est, au besoin, automatiquement créé.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par SCC LifeCycle, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à SCC LifeCycle, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **SCC LifeCycle**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

