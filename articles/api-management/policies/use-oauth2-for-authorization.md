---
title: "Exemple de la stratégie de Gestion des API Azure - Utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal | Microsoft Docs"
description: "Exemple de la stratégie de Gestion des API Azure - Montre comment utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal. Il montre comment obtenir un jeton d’accès d’AAD et le transférer au serveur principal."
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
ms.openlocfilehash: e0aeec66f23033f916c782c8a895e725b0735b62
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal. Il montre comment obtenir un jeton d’accès d’AAD et le transférer au serveur principal. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

