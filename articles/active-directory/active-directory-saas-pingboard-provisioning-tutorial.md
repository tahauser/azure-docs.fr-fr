---
title: "Didacticiel : Configurer Pingboard pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Pingboard."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: b1d2e5468aa5b6a10b93ea118969d66789a17f50
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Didacticiel : Configurer Pingboard pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer pour provisionner et annuler le provisionnement automatiquement de comptes utilisateur d’Azure Active Directory (Azure AD) vers Pingboard.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   un locataire Azure AD ;
*   Un [compte professionnel](https://pingboard.com/pricing) de locataire Pingboard 
*   Un compte utilisateur dans Pingboard avec des autorisations d’administrateur 

> [!NOTE] 
> L’intégration du provisionnement Azure AD repose sur [l’API Pingboard](`https://your_domain.pingboard.com/scim/v2`), disponible sur votre compte.

## <a name="assign-users-to-pingboard"></a>Affectation d’utilisateurs à Pingboard

Azure AD utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre du provisionnement automatique des comptes utilisateur, seuls les utilisateurs affectés à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service de provisionnement, vous devez déterminer quels utilisateurs dans Azure AD ont besoin d’accéder à votre application Pingboard. Vous pouvez ensuite affecter ces utilisateurs à votre application Pingboard en suivant les instructions fournies ici :

[Affecter un utilisateur à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Conseils importants pour l’affectation d’utilisateurs à Pingboard

Nous vous recommandons d’affecter un seul utilisateur Azure AD à Pingboard pour tester la configuration du provisionnement. Les autres utilisateurs peuvent être affectés ultérieurement.

## <a name="configure-user-provisioning-to-pingboard"></a>Configuration du provisionnement des utilisateurs sur Pingboard 

Cette section vous guide tout au long de la connexion d’Azure AD à l’API de provisionnement des comptes utilisateur de Pingboard. Vous configurez aussi le service de provisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Pingboard en fonction de l’affectation d’utilisateurs dans Azure AD.

> [!TIP]
> Pour activer l’authentification unique basée sur SAML pour Pingboard, suivez les instructions fournies sur le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment du provisionnement automatique, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Pour configurer le provisionnement automatique de comptes utilisateur sur Pingboard dans Azure AD

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.

2. Si vous avez déjà configuré Pingboard pour l’authentification unique, recherchez votre instance Pingboard à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Pingboard** dans la galerie d’applications. Sélectionnez **Pingboard** dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance Pingboard, puis sélectionnez l’onglet **Provisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5. Dans la section **Informations d’identification de l’administrateur**, effectuez les étapes suivantes :

    a. Dans **URL de locataire**, entrez `https://your_domain.pingboard.com/scim/v2` et remplacez « your_domain » par votre domaine réel.

    b. Connectez-vous à [Pingboard](https://pingboard.com/) à l’aide de votre compte d’administrateur.

    c. Sélectionnez **Modules complémentaires** > **Intégrations** > **Azure Active Directory**.

    d. Accédez à l’onglet **Configurer** et sélectionnez **Enable user provisioning from Azure** (Activer le provisionnement d’utilisateurs à partir d’Azure).

    e. Copiez le jeton dans **Jeton du porteur OAuth** et entrez-le dans **Jeton secret**.

6. Sur le portail Azure, sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Pingboard. Si la connexion échoue, vérifiez que votre compte Pingboard dispose des autorisations d’administrateur et recommencez l’étape **Tester la connexion**.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement dans **E-mail de notification**. Cochez la case située dessous.

8. Sélectionnez **Enregistrer**. 

9. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to Pingboard** (Synchroniser les utilisateurs Azure Active Directory avec Pingboard).

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur à synchroniser entre Azure AD et Pingboard. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Pingboard pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications. Pour plus d’informations, consultez [Personnalisation des mappages d’attributs de provisionnement d’utilisateurs](active-directory-saas-customizing-attribute-mappings.md).

11. Pour activer le service de provisionnement Azure AD pour Pingboard, définissez le paramètre **État de provisionnement** sur **Activé** dans la section **Paramètres**.

12. Cliquez sur **Enregistrer** pour lancer la synchronisation initiale des utilisateurs affectés à Pingboard.

La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Utilisez la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement. Les journaux décrivent toutes les actions effectuées par le service de provisionnement dans votre application Pingboard.

Pour savoir plus en détail comment lire les journaux d’attribution Azure AD, consultez la section [Générer un état sur l’attribution automatique de comptes d’utilisateurs](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-pingboard-tutorial.md)
