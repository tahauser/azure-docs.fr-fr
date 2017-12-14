---
title: "Comment définir ou modifier des stratégies dans Gestion des API Azure | Microsoft Docs"
description: "Cette rubrique montre comment définir ou modifier des stratégies dans Gestion des API Azure."
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
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 409069cbc382610a48139df75f0f64b1682d8ee6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Comment définir ou modifier des stratégies dans Gestion des API Azure

La définition de la stratégie est un document XML qui décrit une séquence d’instructions entrantes et sortantes. Le code XML peut être modifié directement dans la fenêtre de définition. Vous pouvez également sélectionner une stratégie prédéfinie dans la liste fournie à droite de la fenêtre de la stratégie. Les instructions applicables à l’étendue actuelle sont activées et mises en surbrillance. Lorsque vous cliquez sur une instruction active, le code XML correspondant est inséré à l’emplacement du curseur dans la fenêtre de définition. 

Pour des informations détaillées sur les stratégies, consultez la page [Stratégies dans Gestion des API Azure](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Définir ou modifier une stratégie

Pour définir ou modifier une stratégie, procédez comme suit :

1. Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).
2. Accédez à votre instance APIM.
3. Cliquez sur l’onglet **API**.
4. Sélectionnez l’une des API que vous avez importées précédemment.
5. Sélectionnez l’onglet **Conception**.
6. Sélectionnez une opération à laquelle vous souhaitez appliquer la stratégie. Si vous souhaitez appliquer la stratégie à toutes les opérations, sélectionnez **Toutes les opérations**.
7. Cliquez sur le triangle en regard des crayons **entrant** ou **sortant**.
8. Sélectionnez l’élément **Éditeur de code**.

    ![Modifier la stratégie](./media/set-edit-policies/set-edit-policies01.png)

9. Collez le code de la stratégie de votre choix dans l’une des zones appropriées.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>Configurer l’étendue

Les stratégies peuvent être configurées de façon globale, ou bien au niveau de l’étendue d’un produit, d’une API ou d’une opération. Pour configurer une stratégie, vous devez d’abord sélectionner l’étendue à laquelle elle doit s’appliquer.

Les étendues de stratégie sont évaluées dans l’ordre suivant :

1. Étendue globale
2. Étendue produit
3. Étendue API
4. Étendue opération

Les instructions dans les stratégies sont évaluées en fonction de l’emplacement de l’élément `base`, s’il est présent. Une stratégie globale n’a aucune stratégie parente et l’utilisation de l’élément `<base>` n’a aucun effet.

Pour afficher les stratégies dans l'étendue actuelle dans l'éditeur de stratégie, cliquez sur **Recalculer la stratégie en vigueur pour l'étendue sélectionnée**.

### <a name="global-scope"></a>Étendue globale

L’étendue globale est configurée pour **toutes les API** dans votre instance APIM.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre instance APIM.
2. Cliquez sur **Toutes les API**.

    ![Étendue globale](./media/api-management-howto-policies/global-scope.png)

3. Cliquez sur l’icône de triangle.
4. Sélectionnez **Éditeur de code**.
5. Ajoutez ou modifiez des stratégies.
6. Appuyez sur **Enregistrer**. 

    Les modifications sont propagées immédiatement dans la passerelle de Gestion des API.

### <a name="product-scope"></a>Étendue produit

L’étendue du produit est configurée pour le produit sélectionné.

1. Cliquez sur **Produits**.

    ![Étendue produit](./media/api-management-howto-policies/product-scope.png)

2. Sélectionnez le produit auquel vous souhaitez appliquer des stratégies.
3. Cliquez sur **Stratégies**.
4. Ajoutez ou modifiez des stratégies.
5. Appuyez sur **Enregistrer**. 

### <a name="api-scope"></a>Étendue API

L’étendue de l’API est configurée pour **toutes les opérations** de l’API sélectionnée.

1. Sélectionnez **l’API** à laquelle vous souhaitez appliquer des stratégies.

    ![Étendue API](./media/api-management-howto-policies/api-scope.png)

2. Sélectionnez **Toutes les opérations**.
3. Cliquez sur l’icône de triangle.
4. Sélectionnez **Éditeur de code**.
5. Ajoutez ou modifiez des stratégies.
6. Appuyez sur **Enregistrer**. 

### <a name="operation-scope"></a>Étendue opération 

L’étendue de l’opération est configurée pour l’opération sélectionnée.

1. Sélectionnez une **API**.
2. Sélectionnez l’opération à laquelle vous souhaitez appliquer des stratégies.

    ![Étendue opération](./media/api-management-howto-policies/operation-scope.png)

3. Cliquez sur l’icône de triangle.
4. Sélectionnez **Éditeur de code**.
5. Ajoutez ou modifiez des stratégies.
6. Appuyez sur **Enregistrer**. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les rubriques associées suivantes :

+ [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
+ Pour obtenir la liste complète des instructions et des paramètres de stratégie, consultez [Stratégies API Management](api-management-policy-reference.md).
+ [API Management policy samples](policy-samples.md) (Exemples de stratégie de gestion d’API)