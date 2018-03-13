---
title: "Utilisation des propriétés dans les stratégies Gestion des API Azure"
description: "Découvrez comment utiliser les propriétés dans les stratégies Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Utilisation des propriétés dans les stratégies Gestion des API Azure
Les stratégies Gestion des API sont une fonctionnalité puissante du système qui permet au portail Azure de modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions de la stratégie peuvent être construites à l’aide de valeurs de texte littéral, d’expressions de stratégie et de propriétés. 

Chaque instance du service Gestion des API possède une collection de propriétés de paires clé/valeur qui s’appliquent à l’instance de service. Ces propriétés peuvent être utilisées pour gérer les valeurs de chaîne constantes dans l’ensemble des stratégies et de la configuration de l’API. Chaque propriété peut avoir les attributs suivants :

| Attribut | type | Description |
| --- | --- | --- |
| Nom complet |chaîne |Chaîne alphanumérique utilisée pour référencer la propriété dans les stratégies. |
| Valeur |chaîne |Valeur de la propriété. Elle ne peut pas être vide ni se composer uniquement d’espaces blancs. |
|Secret|booléenne|Détermine si la valeur est un secret et doit être chiffrée.|
| Balises |tableau de chaînes |Balises facultatives qui, lorsqu’elles sont fournies, peuvent être utilisées pour filtrer la liste de propriétés. |

![Valeurs nommées](./media/api-management-howto-properties/named-values.png)

Les valeurs de propriété peuvent contenir des chaînes littérales et des [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx). Par exemple, la valeur de `ExpressionProperty` est une expression de stratégie qui retourne une chaîne contenant la date et l’heure actuelles. La propriété `ContosoHeaderValue` est marquée en tant que secret. Sa valeur ne s’affiche donc pas.

| NOM | Valeur | Secret | Balises |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateHeure.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Pour ajouter et modifier une propriété

![Ajouter une propriété](./media/api-management-howto-properties/add-property.png)

1. Sélectionnez **API** sous **Gestion des API**.
2. Sélectionnez **Valeurs nommées**.
3. Appuyez sur **+ Ajouter**.

  Nom et Valeur doivent être renseignés. Si la valeur de propriété est un secret, cochez la case Il s’agit d’une clé secrète. Entrez une ou plusieurs balises facultatives pour aider à organiser vos propriétés, puis cliquez sur Enregistrer.
4. Cliquez sur **Créer**.

Une fois que la propriété est créée, vous pouvez le modifier en cliquant sur la propriété. Si vous modifiez le nom de propriété, toutes les stratégies qui font référence à cette propriété sont automatiquement mises à jour pour utiliser le nouveau nom.

Pour plus d’informations sur la modification d’une propriété à l’aide de l’API REST, consultez [Modifier une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Pour supprimer une propriété

Pour supprimer une propriété, cliquez sur **Supprimer** en regard de la propriété à supprimer.

> [!IMPORTANT]
> Si la propriété est référencée par des stratégies, vous ne pouvez pas la supprimer correctement tant que vous ne l’avez pas supprimée de toutes les stratégies qui l’utilisent.
> 
> 

Pour plus d’informations sur la suppression d’une propriété à l’aide de l’API REST, consultez [Supprimer une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Pour rechercher et filtrer les propriétés

L’onglet **Valeurs nommées** inclut des fonctionnalités de recherche et de filtrage pour vous aider à gérer vos propriétés. Pour filtrer la liste des propriétés par nom de propriété, entrez un terme à rechercher dans la zone de texte **Propriétés de recherche** . Pour afficher toutes les propriétés, effacez la zone de texte **Propriétés de recherche** , puis appuyez sur Entrée.

Pour filtrer la liste des propriétés en fonction des valeurs de balise, entrez une ou plusieurs balises dans la zone de texte **Filtrer par balises** . Pour afficher toutes les propriétés, effacez la zone de texte **Filtrer par balises** , puis appuyez sur Entrée.

## <a name="to-use-a-property"></a>Pour utiliser une propriété

Pour utiliser une propriété dans une stratégie, placez le nom de la propriété dans une paire d’accolades telle que `{{ContosoHeader}}`, comme illustré dans l’exemple suivant :

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Dans cet exemple, `ContosoHeader` est utilisé comme nom d’en-tête d’une stratégie `set-header`, et `ContosoHeaderValue` comme valeur de cet en-tête. Lorsque cette stratégie est évaluée lors d’une demande ou d’une réponse à la passerelle Gestion des API, `{{ContosoHeader}}` et `{{ContosoHeaderValue}}` sont remplacés par leurs valeurs de propriété respectives.

Les propriétés peuvent être utilisées en tant qu’attribut complet ou valeurs d’élément, comme indiqué dans l’exemple précédent. Elles peuvent également être insérées dans ou combinés avec une partie d’une expression de texte littéral, comme illustré dans l’exemple suivant : `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Les propriétés peuvent également contenir des expressions de stratégie. Dans l’exemple suivant, `ExpressionProperty` est utilisé.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Lorsque cette stratégie est évaluée, `{{ExpressionProperty}}` est remplacé par sa valeur : `@(DateTime.Now.ToString())`. Étant donné que la valeur est une expression de stratégie, l’expression est évaluée et la stratégie poursuit son exécution.

Vous pouvez tester cette opération dans le portail des développeurs en appelant une opération qui a une stratégie dont l’étendue inclut des propriétés. Dans l’exemple suivant, une opération est appelée avec les deux stratégies `set-header` de l’exemple précédent incluant des propriétés. Notez que la réponse contient deux en-têtes personnalisés configurés à l’aide de stratégies et de propriétés.

![Portail des développeurs][api-management-send-results]

Si vous examinez le [suivi de l’inspecteur d’API](api-management-howto-api-inspector.md) pour un appel qui inclut les deux exemples de stratégies précédents incluant des propriétés, vous pouvez voir les deux stratégies `set-header` avec les valeurs de propriété insérées, ainsi que l’évaluation d’expression de stratégie pour la propriété contenant l’expression de stratégie.

![Suivi de l’inspecteur d’API][api-management-api-inspector-trace]

Alors que les valeurs de propriété peuvent contenir des expressions de stratégie, elles ne peuvent pas contenir d’autres propriétés. Si le texte contenant une référence de propriété est utilisé pour une valeur de propriété, comme `Property value text {{MyProperty}}`, cette référence de propriété n’est pas remplacée et est incluse dans la valeur de propriété.

## <a name="next-steps"></a>étapes suivantes
* En savoir plus sur l’utilisation des stratégies
  * [Stratégies dans Gestion des API](api-management-howto-policies.md)
  * [Référence de stratégie](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

