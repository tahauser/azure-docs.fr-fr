---
title: "Exemple de la stratégie de Gestion des API Azure - Définir la durée du cache de réponse | Microsoft Docs"
description: "Exemple de stratégie de Gestion des API Azure - Montre comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal..."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 8471b51999159c8f6233abbbce998b122163efd8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="set-response-cache-duration"></a>Définir la durée du cache de réponse

Cet article montre un exemple de stratégie de Gestion des API Azure qui illustre comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **entrant**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

