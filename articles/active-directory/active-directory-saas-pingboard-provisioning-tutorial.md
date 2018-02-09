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
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Didacticiel : Configurer Pingboard pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer pour approvisionner et retirer automatiquement des comptes utilisateur d’Azure Active Directory vers Pingboard.

## <a name="prerequisites"></a>configuration requise

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory
*   Un [compte professionnel](https://pingboard.com/pricing) de locataire Pingboard 
*   Un compte utilisateur dans Pingboard avec des autorisations d’administrateur 

> [!NOTE] 
> L’intégration de l’approvisionnement Azure Active Directory repose sur l’API Pingboard (`https://your_domain.pingboard.com/scim/v2`), disponible sur votre compte.

## <a name="assigning-users-to-pingboard"></a>Affectation d’utilisateurs à Pingboard

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes utilisateur, seuls les utilisateurs qui ont été « affectés »à une application dans Azure Active Directory sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs dans Azure Active Directory représentent les utilisateurs qui ont besoin d’accéder à votre application Pingboard. Une fois que vous avez effectué votre choix, vous pouvez affecter ces utilisateurs à votre application Pingboard en suivant les instructions fournies ici :

[Affecter un utilisateur à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Conseils importants pour l’affectation d’utilisateurs à Pingboard

*   Nous vous recommandons d’affecter un seul utilisateur Azure Active Directory à Pingboard pour tester la configuration de l’approvisionnement. Les autres utilisateurs peuvent être affectés ultérieurement.

## <a name="configuring-user-provisioning-to-pingboard"></a>Configuration de l’approvisionnement des utilisateurs sur Pingboard 

Cette section explique comment connecter Azure Active Directory à l’API d’approvisionnement de comptes utilisateur Pingboard et configurer le service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Pingboard, en fonction des attributions d’utilisateurs dans Azure Active Directory.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Pingboard en suivant les instructions fournies sur le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Pour configurer l’approvisionnement automatique de comptes utilisateur sur Pingboard dans Azure Active Directory :

1)  Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2) Si vous avez déjà configuré Pingboard pour l’authentification unique, recherchez votre instance Pingboard à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Pingboard** dans la galerie d’applications. Sélectionnez **Pingboard** dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3)  Sélectionnez votre instance Pingboard, puis sélectionnez l’onglet **Approvisionnement**.

4)  Définissez le **Mode d’approvisionnement** sur **Automatique**.

![Approvisionnement de Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) Dans la section Informations d’identification de l’administrateur, effectuez les étapes suivantes :

* Dans la zone de texte **URL de locataire**, entrez `https://your_domain.pingboard.com/scim/v2` et remplacez your_domain par votre domaine réel
* Connectez-vous à [Pingboard](https://pingboard.com/) à l’aide du compte administrateur.
* Cliquez sur Modules complémentaires > Intégrations > Azure Active Directory.
* Cliquez sur l’onglet Configurer et sélectionnez **Enable user provisioning from Azure** (Activer l’approvisionnement d’utilisateurs à partir d’Azure).
* Copiez le contenu du champ **OAuth Bearer Token** (Jeton de porteur OAuth), puis collez-le dans la zone de texte **Jeton secret**.

6) Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure Active Directory peut se connecter à votre application Pingboard. Si la connexion échoue, vérifiez que votre compte Pingboard dispose des autorisations d’administrateur et recommencez l’étape **« Tester la connexion »**.

7) Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case suivante.

8) Cliquez sur **Enregistrer**. 

9) Dans la section Mappages, sélectionnez **Synchronize Azure Active Directory Users to Pingboard** (Synchroniser les utilisateurs Azure Active Directory avec Pingboard).

10) Dans la section **Mappages d’attributs**, passez en revue les attributs d’utilisateur qui seront synchronisés entre Azure Active Directory et Pingboard. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Pingboard pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications. Pour plus d’informations, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](active-directory-saas-customizing-attribute-mappings.md).

11) Pour activer le service d’approvisionnement Azure Active Directory pour Pingboard, définissez **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

12) Cliquez sur **Enregistrer** pour lancer la synchronisation initiale des utilisateurs affectés à Pingboard.

La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 20 minutes, tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement dans votre application Pingboard.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure Active Directory, consultez [Didacticiel : Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-pingboard-tutorial.md)
