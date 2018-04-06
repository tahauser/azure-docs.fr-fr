---
title: Créer, gérer et sécuriser des clés API d’administration et de requête pour la Recherche Azure | Microsoft Docs
description: Les clés API contrôlent l’accès au point de terminaison de service. Les clés d’administration accordent un accès en écriture. Les clés de requête peuvent être créées pour l’accès en lecture seule.
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: ab04214dc02f337afdf8268b7d05b5e0896efc73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Créer et gérer des clés API pour un service Recherche Azure

Toutes les demandes adressées à votre service de recherche ont besoin d’une clé API générée spécialement pour votre service. Cette clé API constitue le seul mécanisme d’authentification de l’accès au point de terminaison de votre service de recherche. 

Une clé API est une chaîne composée de nombres et de lettres générée de manière aléatoire. Par le biais des [autorisations basées sur le rôle](search-security-rbac.md), vous pouvez supprimer ou lire les clés, mais vous ne pouvez pas remplacer une clé avec un mot de passe défini par l’utilisateur ou utiliser Active Directory en tant que méthode d’authentification principale pour accéder aux opérations de recherche. 

Deux types de clés sont utilisés pour accéder à votre service de recherche : administration (lecture-écriture) et requête (lecture seule).

|Clé|Description|limites|  
|---------|-----------------|------------|  
|Admin|Accorde des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données.<br /><br /> Deux clés d’administration, appelées clés *principale* et *secondaire* dans le portail, sont générées quand le service est créé et peuvent être régénérées individuellement à la demande. La possession de deux clés permet de substituer une clé quand l’autre est utilisée pour un accès continu au service.<br /><br /> Les clés d’administration sont spécifiées uniquement dans les en-têtes de requête HTTP. Vous ne pouvez pas insérer de clé API d’administration dans une URL.|2 max. par service|  
|Requête|Accorde un accès en lecture seule aux index et aux documents. Ces clés sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.<br /><br /> Les clés de requête sont créées à la demande. Vous pouvez les créer manuellement dans le portail ou par programme via l’[API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Les clés de requête peuvent être spécifiées dans un en-tête de requête HTTP pour les opérations de recherche, de suggestion ou de consultation. Vous pouvez également transmettre une clé de requête en tant que paramètre pour une URL. Selon la façon dont votre application cliente formule la demande, il peut être plus facile de transmettre la clé en tant que paramètre de requête :<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 par service|  

 Visuellement, il n’existe aucune distinction entre une clé d’administration et une clé de requête. Les deux clés sont des chaînes composées de 32 caractères alphanumériques générés de façon aléatoire. Si vous n’êtes pas sûr du type de clé spécifié dans votre application, vous pouvez [vérifier les valeurs de clé dans le portail](https://portal.azure.com) ou utiliser l’[API REST](https://docs.microsoft.com/rest/api/searchmanagement/) pour retourner la valeur et le type de clé.  

> [!NOTE]  
>  L’insertion de données sensibles comme une `api-key` dans l’URI de requête est considérée comme une pratique peu sécurisée. C’est pourquoi Recherche Azure accepte uniquement une clé de requête sous forme de `api-key` dans la chaîne de requête, et il est conseillé de procéder autrement, sauf si le contenu de l'index doit être accessible au public. En règle générale, nous vous recommandons de transmettre votre `api-key` en tant qu'en-tête de demande.  

## <a name="find-api-keys-for-your-service"></a>Rechercher des clés API pour votre service

Vous pouvez obtenir les clés d’accès dans le portail ou via l’[API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/). Pour plus d’informations, consultez la page [Gérer les clés API d’administration et de requête](search-security-api-keys.md).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Répertoriez les [services de recherche](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pour votre abonnement.
3. Sélectionnez le service, puis sur la page du service, recherchez **Paramètres** >**Clés** pour afficher les clés d’administration et de requête.

![Page du portail, section Paramètres\Clés](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Régénération des clés d’administration

Deux clés d’administration sont créées pour chaque service afin que vous puissiez substituer la clé principale et utiliser la clé secondaire pour un accès permanent.

Si vous régénérez les clés principale et secondaire en même temps, les applications utilisant l’une des clés pour accéder aux opérations de service n’ont plus accès au service.

1. Dans la page **Paramètres** >**Clés**, copiez la clé secondaire.
2. Pour toutes les applications, mettez à jour les paramètres de la clé API afin d’utiliser la clé secondaire.
3. Régénérez la clé principale.
4. Mettez à jour toutes les applications pour qu’elles utilisent la nouvelle clé principale.

## <a name="secure-api-keys"></a>Sécuriser les clés API
La sécurité des clés est assurée en limitant l’accès via le portail ou des interfaces Resource Manager (PowerShell ou interface de ligne de commande). Comme indiqué, les administrateurs des abonnements peuvent afficher et régénérer toutes les clés API. Par précaution, passez en revue les affectations de rôle pour comprendre qui a accès aux clés Admin.

+ Dans le tableau de bord de service, cliquez sur **Contrôle d’accès (IAM)** pour afficher les affectations de rôle pour votre service.

Les membres des rôles suivants peuvent afficher et régénérer les clés : Propriétaire, Collaborateur, [Collaborateurs Search Service](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#search-service-contributor)

> [!Note]
> Pour un accès en fonction de l’identité sur les résultats de recherche, vous pouvez créer des filtres de sécurité pour ajuster les résultats par identité, en supprimant les documents auxquels le demandeur ne doit pas avoir accès. Pour plus d’informations, consultez [Filtres de sécurité](search-security-trimming-for-azure-search.md) et [Sécuriser avec Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Voir aussi

+ [Contrôle d’accès en fonction du rôle dans la Recherche Azure](search-security-rbac.md)
+ [Gestion à l’aide de PowerShell](search-manage-powershell.md) 
+ [Article sur les performances et l’optimisation](search-performance-optimization.md)