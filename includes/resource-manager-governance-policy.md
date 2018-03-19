---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
[Les stratégies Azure](/azure/azure-policy/) vous aide à faire en sorte que toutes les ressources de l’abonnement répondent aux standards des entreprises. Utilisez des stratégies pour réduire les coûts en limitant les options de déploiement uniquement aux types de ressources et références SKU approuvés. Vous définissez les règles et les actions pour vos ressources. Ces règles sont ensuite automatiquement appliquées lors du déploiement. Par exemple, vous pouvez contrôler les types de ressources qui sont déployés. Vous pouvez aussi restreindre les emplacements approuvés pour les ressources. Certaines stratégies refuse une action, tandis que d’autres définissent l’audit d’une action.

La stratégie est complémentaire au contrôle d’accès en fonction du rôle (RBAC). Le contrôle d’accès en fonction du rôle se concentre sur l’accès utilisateur, et constitue un système de refus par défaut et d’autorisation explicite. La stratégie se focalise sur les propriétés des ressources pendant et après le déploiement. Elle est, par défaut, un système explicite d’autorisation et de refus.

Il y a deux concepts à comprendre concernant les stratégies : les *définitions de stratégie* et les *attributions de stratégie*. Une définition de stratégie décrit les conditions de gestion que vous voulez appliquer. Une attribution de stratégie fait appliquer une définition de stratégie à une portée particulière.

![Attribuer des stratégies](./media/resource-manager-governance-policy/policy-concepts.png)

Azure fournit plusieurs définitions de stratégie intégrées que vous pouvez utiliser sans avoir à faire de modifications. Vous entrez des valeurs de paramètre pour spécifier celles qui sont autorisées dans votre portée. Si la définition de stratégie intégrée ne répond pas à vos critères, vous pouvez [créer des définitions de stratégie personnalisées](../articles/azure-policy/create-manage-policy.md).
