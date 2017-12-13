---
title: "Exemples de stratégie dans la Gestion des API Azure | Microsoft Docs"
description: "Découvrez les stratégies disponibles dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: apimpm
ms.openlocfilehash: 0e8089cbcc5e38504d6b4c7ced372781f9a5e6d8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-policy-samples"></a>Exemples de stratégie dans la Gestion des API

Les [stratégies](api-management-howto-policies.md) sont une fonctionnalité puissante du système qui permet à l’éditeur de changer le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Le tableau suivant contient des liens vers des exemples et donne une brève description de chaque exemple.

|||
|---|---|
|**Stratégies de trafic entrant**||
|[Ajouter un en-tête Forwarded pour autoriser l’API de service principal à concevoir des URL correctes](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Montre comment ajouter un en-tête Forwarded à la demande entrante pour autoriser l’API de service principal à construire des URL correctes.|
|[Ajouter un en-tête contenant un ID de corrélation](./policies/add-correlation-id.md?toc=api-management/toc.json) |Montre comment ajouter un en-tête contenant un ID de corrélation à la requête entrante.|
|[Ajouter des fonctionnalités à un service principal et mettre en cache la réponse](./policies/cache-response.md?toc=api-management/toc.json) |Montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques.|
|[Autoriser l’accès basé sur les revendications JWT](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT.|
|[Autoriser l’accès à l’aide du jeton Google OAuth](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Montre comment autoriser l’accès à vos points de terminaison en utilisant Google comme fournisseur de jeton OAuth.|
|[Générer une signature d’accès partagé et transférer la demande vers le stockage Azure](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Montre comment générer une [signature d’accès partagé](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) à l’aide d’expressions et transférer la demande vers le stockage Azure avec une stratégie rewrite-uri. |
|[Obtenir un jeton d’accès OAuth2 d’AAD et le transférer au backend](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Fournit un exemple d’utilisation d’OAuth2 pour l’autorisation entre la passerelle et un backend. Il montre comment obtenir un jeton d’accès OAuth2 d’AAD et le transférer au backend.|
|[Obtenir un jeton X-CSRF de la passerelle SAP à l’aide d’une stratégie send-request](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Montre comment implémenter un modèle X-CSRF utilisé par de nombreuses API. Cet exemple est spécifique de la passerelle SAP. |
|[Router la demande en fonction de la taille de son corps](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Montre comment router les demandes en fonction de la taille de leurs corps.|
|[Envoyer des informations de contexte de demande au backend](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Montre comment envoyer des informations de contexte au backend à des fins de journalisation ou de traitement.|
|[Définir la durée du cache de réponse](./policies/set-cache-duration.md?toc=api-management/toc.json) |Montre comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le backend.|
|**Stratégies de trafic sortant**||
|[Filtrer le contenu de la réponse](./policies/filter-response-content.md?toc=api-management/toc.json) | Montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la demande.|
|**Stratégies en cas d’erreur**||
|[Journaliser les erreurs dans Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Montre comment ajouter une stratégie de journalisation des erreurs pour envoyer les erreurs à Stackify à des fins de journalisation.|
