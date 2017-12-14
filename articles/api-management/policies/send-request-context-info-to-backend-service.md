---
title: "Exemple de la stratégie de Gestion des API Azure - Envoyer des informations de contexte de requête au service principal | Microsoft Docs"
description: "Exemple de la stratégie de Gestion des API Azure - Montre comment envoyer des informations de contexte de requête au service principal."
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
ms.openlocfilehash: af4d0a8aa3705b290fa5e4801eaa25264229696e
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="send-request-context-information-to-the-backend-service"></a>Envoyer des informations de contexte de requête au service principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment envoyer des informations de contexte de requête au service principal. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

