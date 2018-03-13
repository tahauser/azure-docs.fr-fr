---
title: "Caractéristiques de l’interaction des locataires Azure Active Directory | Microsoft Docs"
description: "Gérer vos locataires Azure Active Directory en les considérant comme des ressources entièrement indépendantes"
services: active-tenant
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 2c00f382546a0e5f1e9da9dc90777dcf9622ec94
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Comprendre l’interaction entre plusieurs locataires Azure Active Directory

Dans Azure Active Directory (Azure AD), chaque locataire est une ressource entièrement indépendante, c’est-à-dire un homologue logiquement indépendant des autres locataires que vous gérez. Il n’existe pas de relation parent-enfant entre les locataires. Cette indépendance entre les locataires vaut pour les ressources, l’administration et la synchronisation.

## <a name="resource-independence"></a>Indépendance des ressources
* Si vous créez ou supprimez une ressource dans un locataire, cela n’a aucun effet sur les ressources d’un autre locataire, si l’on excepte le cas des utilisateurs externes. 
* Si vous utilisez l’un de vos noms de domaine avec un locataire, il ne peut être utilisé avec aucun autre locataire.

## <a name="administrative-independence"></a>Indépendance de l’administration
Si un utilisateur non administrateur du locataire « Contoso » crée le locataire de test « Test », alors :

* Par défaut, l’utilisateur qui crée un locataire est ajouté comme utilisateur externe dans ce nouveau locataire et se voit attribuer rôle d’administrateur général dans ce locataire.
* Les administrateurs du locataire « Contoso » n’ont pas de privilèges d’administration directs sur le locataire « Test », à moins qu’un administrateur de « Test » leur ait spécifiquement accordé ces privilèges. Cependant, les administrateurs de « Contoso » peuvent contrôler l’accès au locataire « Test » s’ils contrôlent le compte d’utilisateur qui a créé « Test ».
* Si vous ajoutez et/ou supprimez un rôle d’administrateur pour un utilisateur dans un locataire, la modification n’a aucun impact sur les rôles d’administrateur que l’utilisateur possède dans une autre locataire.

## <a name="synchronization-independence"></a>Indépendance de la synchronisation
Vous pouvez configurer chaque locataire Azure AD de manière indépendante de sorte que les données soient synchronisées à partir d’une même instance de :

* l’outil Azure AD Connect, pour synchroniser les données avec une seule forêt AD ;
* Azure Active Directory Connector pour Forefront Identity Manager, pour synchroniser les données avec une ou plusieurs forêts locales et/ou des sources de données non Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Ajouter un locataire Azure AD
Pour ajouter un locataire Azure AD dans le portail Azure, connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur général Azure AD et, sur la gauche, sélectionnez **Nouveau**.

> [!NOTE]
> Contrairement aux autres ressources Azure, vos locataires ne sont pas des ressources enfants d’un abonnement Azure. Si votre abonnement Azure est annulé ou qu’il est arrivé à expiration, vous pouvez toujours accéder aux données de votre locataire via Azure PowerShell, l’API Azure Graph ou le centre d’administration Office 365. Vous pouvez aussi [associer un autre abonnement au locataire](active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une vue d’ensemble des problèmes de licence Azure AD et découvrir les bonnes pratiques, consultez [Qu’est-ce que la gestion des licences de locataires Azure Active Directory ?](active-directory-licensing-whatis-azure-portal.md).
