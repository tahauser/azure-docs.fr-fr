---
title: "Ajout de la mise en cache pour améliorer les performances de Gestion des API Azure | Microsoft Docs"
description: "Apprenez à améliorer la latence, la consommation de bande passante et la charge du service web pour les appels du service Gestion des API."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7458ad6e0a864d742f74ce743ce3179594113c00
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Ajout de mise en cache pour améliorer les performances dans Gestion des API Azure
Les opérations dans Gestion des API Azure peuvent être configurées pour mettre en cache la réponse. La mise en cache de la réponse peut réduire de façon importante la latence de l'API, la consommation de bande passante et la charge du service web pour les données qui ne changent pas fréquemment.
 
Pour plus d’informations sur la mise en cache, consultez [Stratégies de mise en cache dans Gestion des API](api-management-caching-policies.md) et [Mise en cache personnalisée dans Gestion des API Azure](api-management-sample-cache-by-key.md).

![stratégies de cache](media/api-management-howto-cache/cache-policies.png)

Ce que vous allez apprendre :

> [!div class="checklist"]
> * Ajouter une mise en cache des réponses pour votre API
> * Vérifier l’action de mise en cache

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

+ [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
+ [Importer et publier une API](import-and-publish.md)

## <a name="caching-policies"> </a>Ajouter les stratégies de mise en cache

Avec les stratégies de mise en cache montrées dans cet exemple, la première requête envoyée à l’opération **GetSpeakers** renvoie une réponse du service principal. Cette réponse est mise en cache, du fait des en-têtes et des paramètres de chaîne de requête spécifiés. Les autres appels à l'opération comportant des paramètres correspondants recevront la réponse mise en cache, jusqu'à expiration de la durée de mise en cache.

1. Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).
2. Accédez à votre instance APIM.
3. Sélectionnez l’onglet **API**.
4. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
5. Sélectionnez **GetSpeakers**.
6. Sélectionnez l’onglet **Conception** en haut de l’écran.
7. Dans la fenêtre **Traitement entrant**, cliquez sur le triangle (à côté du crayon).

    ![éditeur de code](media/api-management-howto-cache/code-editor.png)
8. Sélectionnez **Éditeur de Code**.
9. Dans l’élément **entrant**, ajoutez la stratégie suivante :

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

10. Dans l’élément **sortant**, ajoutez la stratégie suivante :

        <cache-store caching-mode="cache-on" duration="20" />

    **Durée** spécifie l'intervalle d'expiration des réponses mises en cache. Dans cet exemple, l’intervalle est de **20** secondes.

## <a name="test-operation"></a>Appel d’une opération et test de la mise en cache
Pour voir la mise en cache en action, appelez l'opération depuis le portail des développeurs.

1. Dans le portail Azure, accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Sélectionnez l’API à laquelle vous avez ajoutée des stratégies de mise en cache.
4. Sélectionnez l’opération **GetSpeakers**.
5. Cliquez sur l’onglet **Test** dans le menu supérieur droit.
6. Appuyez sur **Envoyer**.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les stratégies de mise en cache, voir la section [Stratégies de mise en cache][Caching policies] dans [Référence de stratégie de Gestion des API][API Management policy reference].
* Pour plus d’informations sur la mise en cache des éléments par clé à l’aide d’expressions de stratégie, consultez [Mise en cache personnalisée dans la gestion des API Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
