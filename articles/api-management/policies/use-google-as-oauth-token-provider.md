---
title: "Exemple de stratégie de Gestion des API Azure - Autoriser l’accès à l’aide du jeton Google OAuth | Microsoft Docs"
description: "Exemple de stratégie de Gestion des API Azure - Montre comment autoriser l’accès à vos points de terminaison en utilisant Google comme fournisseur de jeton OAuth."
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
ms.openlocfilehash: 3889c6fcaef0845c05e455ad173c6880ef053892
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="authorize-access-using-google-oauth-token"></a>Autoriser l’accès à l’aide du jeton Google OAuth

Cet article montre un exemple de stratégie de Gestion des API Azure qui illustre comment autoriser l’accès à vos points de terminaison en utilisant Google comme fournisseur de jeton OAuth. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **entrant**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

