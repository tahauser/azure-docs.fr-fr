---
title: Filtres dans les vues de Azure Log Analytics | Microsoft Docs
description: Un filtre dans une vue de Log Analytics permet aux utilisateurs de filtrer les données au moyen d’une valeur d’une propriété particulière, sans modifier la vue proprement dite.  Cet article décrit comment utiliser un filtre et en ajouter un à une vue personnalisée.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="filters-in-log-analytics-views"></a>Filtres dans les vues de Log Analytics
Un **filtre** dans une [vue de Log Analytics](log-analytics-view-designer.md) permet aux utilisateurs de filtrer les données au moyen d’une valeur d’une propriété particulière, sans modifier la vue proprement dite.  Par exemple, vous pouvez autoriser les utilisateurs de votre vue à filtrer l’affichage des données uniquement à partir d’un ordinateur ou d’un ensemble d’ordinateurs particulier.  Vous pouvez créer plusieurs filtres sur une seule et unique vue pour permettre aux utilisateurs d’effectuer un filtrage avec plusieurs propriétés.  Cet article décrit comment utiliser un filtre et en ajouter un à une vue personnalisée.

## <a name="using-a-filter"></a>Utilisation d’un filtre
Cliquez sur **Filtre** pour ouvrir le volet de filtre pour une vue.  Cela vous permet de sélectionner un intervalle de temps et des valeurs pour tous les filtres disponibles pour la vue.  Lorsque vous sélectionnez un filtre, il affiche une liste des valeurs disponibles.  Vous pouvez en sélectionner une ou plusieurs ou bien les saisir. La vue est automatiquement mise à jour en filtrant avec les valeurs spécifiées. 

Si aucune valeur n’est sélectionnée pour un filtre, celui-ci n’est pas appliqué à la vue.  Si vous supprimez toutes les valeurs d’un filtre, celui-ci n’est plus appliqué.


![Exemple de filtre](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>Création d’un filtre

Créez un filtre à partir de l’onglet **Filtres** lors de la [modification d’une vue](log-analytics-view-designer.md).  Le filtre est global pour la vue et s’applique à toutes ses parties.  

![Paramètres de filtre](media/log-analytics-view-designer/filters-settings.png)

Le tableau suivant décrit les paramètres d’un filtre.

| Paramètre | Description |
|:---|:---|
| Nom du champ | Nom du champ utilisé pour le filtrage.  Cela doit correspondre au champ de résumé dans **Requête pour des valeurs**. |
| Requêtes pour des valeurs | Requête à exécuter pour remplir la liste déroulante de filtre pour l’utilisateur.  Cela doit utiliser [Résumer](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) ou [Distinguer](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) pour fournir des valeurs uniques pour un champ particulier et cela doit correspondre au **Nom du champ**.  Vous pouvez utiliser [Tri](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) pour trier les valeurs affichées à l’utilisateur. |
| Tag | Nom du champ qui est utilisé dans les requêtes prenant en charge le filtre et qui s’affiche également à l’utilisateur. |

### <a name="examples"></a>Exemples

Le tableau suivant présente quelques exemples de filtres communs.  

| Nom du champ | Requêtes pour des valeurs | Tag |
|:--|:--|:--|
| Ordinateur   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Ordinateurs |
| EventLevelName | Event &#124; distinct EventLevelName | Niveau de gravité |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Niveau de gravité |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modifier des requêtes de vue

Pour qu’un filtre soit mis en vigueur, vous devez modifier les requêtes dans la vue pour filtrer avec les valeurs sélectionnées.  Si vous ne modifiez pas les requêtes dans la vue, les valeurs sélectionnées par l’utilisateur n’auront aucun effet.

La syntaxe pour l’utilisation d’une valeur de filtre dans une requête est : 

    where ${filter name}  

Par exemple, si votre vue comprend une requête qui retourne des événements et utilise un filtre nommé Computers, vous pouvez utiliser ceci.

    Event | where ${Computers} | summarize count() by EventLevelName

Si vous avez ajouté un autre filtre nommé Severity, vous pouvez utiliser la requête suivante pour utiliser les deux filtres.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [Parties de visualisation](log-analytics-view-designer-parts.md) que vous pouvez ajouter à votre vue personnalisée.