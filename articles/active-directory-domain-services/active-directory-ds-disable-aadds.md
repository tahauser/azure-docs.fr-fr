---
title: "Désactiver Azure Active Directory Domain Services | Microsoft Docs"
description: "Désactiver Azure Active Directory Domain Services à l’aide du portail Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: f61f6df85e47bec32e147990d956a4409429a60c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Désactiver Azure Active Directory Domain Services à l’aide du portail Azure
Cet article explique comment utiliser le portail Azure et désactiver Azure Active Directory (AD) Domain Services pour votre annuaire Azure AD.

> [!WARNING]
> **La suppression est définitive et ne peut pas être annulée.**
> Agissez avec précaution ! Lorsque vous supprimez le domaine managé :
  * Les contrôleurs de domaine pour le domaine managé sont déprovisionnés et supprimés du réseau virtuel.
  * Les données sur le domaine managé sont supprimées définitivement. Cela inclut les unités d’organisation personnalisées, les stratégies de groupe, les enregistrements DNS personnalisés, les principaux de service, les GMSA, etc. que vous avez créés sur le domaine managé.
  * Les machines jointes au domaine managé perdent leur relation d’approbation avec ce domaine et doivent être disjointes de celui-ci.
  * Vous ne pouvez pas vous connecter à ces machines en utilisant les informations d’identification AD (Active Directory) de l’entreprise. Utilisez à la place les informations d’identification du compte d’administrateur local de la machine.
La suppression du domaine managé ne supprime pas votre annuaire Azure AD ni n’a d’impact négatif sur celui-ci.
>

Suivez les étapes décrites ici pour supprimer votre domaine managé Azure AD Domain Services :
1. Accédez à l’[extension Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) dans le portail Azure.
2. Cliquez sur le nom de votre domaine managé.

    ![Sélectionnez le domaine à supprimer.](./media/getting-started/domain-services-delete-select-domain.png)

3. Dans la page **Vue d’ensemble**, cliquez sur le bouton **Supprimer**.

    ![Suppression du domaine](./media/getting-started/domain-services-delete-domain.png)

4. Pour confirmer la suppression, tapez le nom de domaine DNS du domaine managé. Lorsque c’est fait, cliquez sur le bouton **Supprimer**.

    ![Confirmation de la suppression du domaine](./media/getting-started/domain-services-delete-domain-confirm.png)

Il faut compter 15 à 20 minutes environ pour la suppression du domaine managé.

Pensez à [partager vos commentaires](active-directory-ds-contact-us.md) pour nous aider à comprendre quelles fonctionnalités vous aideraient à choisir les services de domaine Azure AD à l’avenir. Ces commentaires nous permettent de faire évoluer le service pour mieux répondre à vos besoins et cas d’utilisation de déploiement.
