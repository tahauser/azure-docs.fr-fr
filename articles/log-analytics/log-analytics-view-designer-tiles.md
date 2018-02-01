---
title: "Référence des vignettes pour le Concepteur de vues dans Azure Log Analytics | Microsoft Docs"
description: "Le Concepteur de vues de Log Analytics permet de créer des vues personnalisées dans le portail Azure, qui contiennent différentes visualisations de données de votre espace de travail Log Analytics. Cet article fournit une référence pour les paramètres de chacune des vignettes utilisables dans vos vues personnalisées."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 9512a3f45ba6b03af4b0c9bee444948381f4fdcb
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="log-analytics-view-designer-tile-reference"></a>Référence de la vignette Concepteur de vues de Log Analytics
Le Concepteur de vues de Log Analytics permet de créer des vues personnalisées dans le portail Azure, qui contiennent différentes visualisations de données de votre espace de travail Log Analytics. Cet article fournit une référence pour les paramètres de chacune des vignettes utilisables dans vos vues personnalisées.

Autres articles disponibles concernant le Concepteur de vues :

* [Concepteur de vues](log-analytics-view-designer.md) - vue d’ensemble du Concepteur de vues et des procédures de création et de modification des vues personnalisées.
* [Référence des composants de visualisation](log-analytics-view-designer-parts.md) - référence des paramètres pour chacune des vignettes utilisables dans vos vues personnalisées.

>[!NOTE]
> Si votre espace de travail a été mis à niveau vers le [nouveau langage de requête Log Analytics](log-analytics-log-search-upgrade.md), les requêtes de toutes les vues doivent être écrites à l’aide du [nouveau langage de requête](https://go.microsoft.com/fwlink/?linkid=856078).  Toutes les vues créées avant la mise à niveau de l’espace de travail sont automatiquement converties.

Le tableau suivant répertorie les différents types de vignettes disponibles dans le Concepteur de vues.  Les sections suivantes décrivent en détail chaque type de vignette et ses propriétés.

| Vignette | DESCRIPTION |
|:--- |:--- |
| [Nombre](#number-tile) |Nombre unique indiquant le décompte des enregistrements retournés par une requête. |
| [Deux nombres](#two-numbers-tile) |Deux nombres uniques indiquant le décompte des enregistrements retournés par deux requêtes distinctes. |
| [Anneau](#donut-tile) |Graphique basé sur une requête avec une valeur de synthèse au centre. |
| [Graphique en courbes et légende](#line-chart-amp-callout-tile) |Graphique en courbes basé sur une requête, et légende avec une valeur de synthèse. |
| [Graphique en courbes](#line-chart-tile) |Graphique en courbes basé sur une requête. |
| [Deux chronologies](#two-timelines-tile) |Histogramme avec deux séries basée chacune sur une requête distincte. |

## <a name="number-tile"></a>Vignette Nombre
La vignette **Nombre** affiche un nombre unique correspondant au nombre d’enregistrements retournés par une requête de journal et une étiquette.

![Vignette Nombre](media/log-analytics-view-designer/tile-number.png)

| Paramètre | DESCRIPTION |
|:--- |:--- |
| NOM |Texte à afficher en haut de la vignette. |
| DESCRIPTION |Texte à afficher sous le nom de la vignette. |
| **Vignette** | |
| Légende |Texte à afficher sous la valeur. |
| Requête |Requête à exécuter.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionner si la vérification du flux de données doit être activée pour la vignette.  Un autre message s’affiche si les données ne sont pas disponibles pour la vignette.  Cette option est généralement utilisée pour fournir un message pendant la période temporaire, lorsque la vue est installée et que des données sont disponibles. |
| Requête |Requête à exécuter pour vérifier si les données sont disponibles pour la vue.  Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message à afficher si la requête de vérification du flux de données ne retourne aucune donnée.  Si vous ne fournissez aucun message, le texte *Exécution de l'évaluation* s’affiche. |


## <a name="two-numbers-tile"></a>Vignette Deux nombres
La vignette **Deux nombres** affiche deux nombres indiquant le décompte des enregistrements retournés par deux requêtes de journal distinctes, et une étiquette pour chacune d’elles.

![Vignette Deux nombres](media/log-analytics-view-designer/tile-two-numbers.png)

| Paramètre | DESCRIPTION |
|:--- |:--- |
| NOM |Texte à afficher en haut de la vignette. |
| DESCRIPTION |Texte à afficher sous le nom de la vignette. |
| **Première vignette** | |
| Légende |Texte à afficher sous la valeur. |
| Requête |Requête à exécuter.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| **Deuxième vignette** | |
| Légende |Texte à afficher sous la valeur. |
| Requête |Requête à exécuter.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionner si la vérification du flux de données doit être activée pour la vignette.  Un autre message s’affiche si les données ne sont pas disponibles pour la vignette.  Cette option est généralement utilisée pour fournir un message pendant la période temporaire, lorsque la vue est installée et que des données sont disponibles. |
| Requête |Requête à exécuter pour vérifier si les données sont disponibles pour la vue.  Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message à afficher si la requête de vérification du flux de données ne retourne aucune donnée.  Si vous ne fournissez aucun message, le texte *Exécution de l'évaluation* s’affiche. |


## <a name="donut-tile"></a>Vignette Anneau
La vignette **Anneau** affiche un nombre unique résumé à partir d’une colonne de valeur dans une requête de journal.  L’anneau affiche sous forme graphique les résultats des trois premiers enregistrements.

![Vignette Anneau](media/log-analytics-view-designer/tile-donut.png)

| Paramètre | DESCRIPTION |
|:--- |:--- |
| NOM |Texte à afficher en haut de la vignette. |
| DESCRIPTION |Texte à afficher sous le nom de la vignette. |
| **Anneau** | |
| Requête |Requête à exécuter pour obtenir l’anneau.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats. |
| **Anneau** |**&gt; Centrer** |
| Texte |Texte à afficher sous la valeur contenue dans l’anneau. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique.<br><br>- Sum : addition des valeurs de tous les enregistrements avec la valeur de propriété.<br>- Percentage : pourcentage des valeurs additionnées des enregistrements avec la valeur de propriété, en comparaison des valeurs additionnées de tous les enregistrements. |
| Valeurs de résultat utilisées dans l’opération relative au centre |Vous pouvez cliquer sur le signe plus (+) pour ajouter une ou plusieurs valeurs.  Les résultats de la requête sont alors limités aux enregistrements dont vous avez spécifié les valeurs de propriété.  Si aucune valeur n’est ajoutée, tous les enregistrements sont inclus dans la requête. |
| **Anneau** |**&gt; Options supplémentaires** |
| Couleurs |Couleur à afficher pour chacune des trois premières propriétés.  Si vous souhaitez spécifier d’autres couleurs pour des valeurs de propriété spécifiques, utilisez l’option Mappage avancé des couleurs. |
| Mappage avancé des couleurs |Affiche une couleur pour des valeurs de propriété spécifiques.  Si la valeur spécifiée figure parmi les trois premières, l’autre couleur s’affiche au lieu de la couleur standard.  Si la propriété ne figure pas parmi les trois premières, la couleur ne s’affiche pas. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionner si la vérification du flux de données doit être activée pour la vignette.  Un autre message s’affiche si les données ne sont pas disponibles pour la vignette.  Cette option est généralement utilisée pour fournir un message pendant la période temporaire, lorsque la vue est installée et que des données sont disponibles. |
| Requête |Requête à exécuter pour vérifier si les données sont disponibles pour la vue.  Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message à afficher si la requête de vérification du flux de données ne retourne aucune donnée.  Si vous ne fournissez aucun message, le texte *Exécution de l'évaluation* s’affiche. |


## <a name="line-chart-tile"></a>Vignette Graphique en courbes
La vignette **Graphique en courbes** affiche un graphique en courbes comprenant plusieurs séries provenant d’une requête de journal dans le temps.  

![Vignette Graphique en courbes et légende](media/log-analytics-view-designer/tile-line-chart.png)

| Paramètre | DESCRIPTION |
|:--- |:--- |
| NOM |Texte à afficher en haut de la vignette. |
| DESCRIPTION |Texte à afficher sous le nom de la vignette. |
| **Graphique en courbes** | |
| Requête |Requête à exécuter pour obtenir le graphique en courbes.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats.  Si la requête utilise le mot clé **interval**, l’axe des abscisses (X) du graphique utilise cet intervalle de temps.  Si la requête n’inclut pas le mot clé **interval**, des intervalles horaires sont utilisés pour l’axe des abscisses (X). |
| **Graphique en courbes** |**&gt; Axe des Y** |
| Utiliser l’échelle logarithmique |Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête.  Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs.  Le **Type d’unité** spécifie la catégorie de l’unité et définit les valeurs de **Type d’unité en cours** disponibles.  Si vous sélectionnez une valeur dans **Convertir en**, les valeurs numériques sont converties du type **Unité actuelle** dans le type **Convertir en**. |
| Étiquette personnalisée |Texte à afficher pour l’axe des X en regard de l’étiquette du type d’unité.  Si aucune étiquette n’est spécifiée, le type d’unité s’affiche. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionner si la vérification du flux de données doit être activée pour la vignette.  Un autre message s’affiche si les données ne sont pas disponibles pour la vignette.  Cette option est généralement utilisée pour fournir un message pendant la période temporaire, lorsque la vue est installée et que des données sont disponibles. |
| Requête |Requête à exécuter pour vérifier si les données sont disponibles pour la vue.  Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message à afficher si la requête de vérification du flux de données ne retourne aucune donnée.  Si vous ne fournissez aucun message, le texte *Exécution de l'évaluation* s’affiche. |


## <a name="line-chart--callout-tile"></a>Vignette Graphique en courbes et légende
La vignette **Graphique en courbes et légende** affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps, et une légende avec une valeur de synthèse.  

![Vignette Graphique en courbes et légende](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Paramètre | DESCRIPTION |
|:--- |:--- |
| NOM |Texte à afficher en haut de la vignette. |
| DESCRIPTION |Texte à afficher sous le nom de la vignette. |
| **Graphique en courbes** | |
| Requête |Requête à exécuter pour obtenir le graphique en courbes.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats.  Si la requête utilise le mot clé **interval**, l’axe des abscisses (X) du graphique utilise cet intervalle de temps.  Si la requête n’inclut pas le mot clé **interval**, des intervalles horaires sont utilisés pour l’axe des abscisses (X). |
| **Graphique en courbes** |**&gt; Légende** |
| Légende |Texte de titre à afficher au-dessus de la valeur de la légende. |
| Nom de la série |Valeur de propriété pour la série à utiliser pour la valeur de la légende.  Si aucune série n’est fournie, tous les enregistrements de la requête sont utilisés. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique pour la légende.<br>- Average : moyenne des valeurs de tous les enregistrements.<br><br>- Count : nombre de tous les enregistrements retournés par la requête.<br>- Last Sample : valeur du dernier intervalle inclus dans le graphique.<br>- Max : valeur maximale des intervalles inclus dans le graphique.<br>-Min : valeur minimale des intervalles inclus dans le graphique.<br>-Sum : somme des valeurs de tous les enregistrements. |
| **Graphique en courbes** |**&gt; Axe des Y** |
| Utiliser l’échelle logarithmique |Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête.  Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs.  Le **Type d’unité** spécifie la catégorie de l’unité et définit les valeurs de **Type d’unité en cours** disponibles.  Si vous sélectionnez une valeur dans **Convertir en**, les valeurs numériques sont converties du type **Unité actuelle** dans le type **Convertir en**. |
| Étiquette personnalisée |Texte à afficher pour l’axe des X en regard de l’étiquette du type d’unité.  Si aucune étiquette n’est spécifiée, le type d’unité s’affiche. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionner si la vérification du flux de données doit être activée pour la vignette.  Un autre message s’affiche si les données ne sont pas disponibles pour la vignette.  Cette option est généralement utilisée pour fournir un message pendant la période temporaire, lorsque la vue est installée et que des données sont disponibles. |
| Requête |Requête à exécuter pour vérifier si les données sont disponibles pour la vue.  Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message à afficher si la requête de vérification du flux de données ne retourne aucune donnée.  Si vous ne fournissez aucun message, le texte *Exécution de l'évaluation* s’affiche. |


## <a name="two-timelines-tile"></a>Vignette Deux chronologies
La vignette **Deux chronologies** affiche les résultats de deux requêtes de journal dans le temps sous la forme de graphiques à barres.  Une légende est affichée pour chaque série.  

![Vignette Deux chronologies](media/log-analytics-view-designer/tile-two-timelines.png)

| Paramètre | DESCRIPTION |
|:--- |:--- |
| NOM |Texte à afficher en haut de la vignette. |
| DESCRIPTION |Texte à afficher sous le nom de la vignette. |
| Premier graphique | |
| Légende |Texte à afficher dans la légende de la première série. |
| Couleur |Couleur à utiliser pour les colonnes de la première série. |
| Requête de graphique |Requête à exécuter pour la première série.  Le décompte du nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes de graphique. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique pour la légende.<br><br>- Average : moyenne des valeurs de tous les enregistrements.<br>- Count : nombre de tous les enregistrements retournés par la requête.<br>- Last Sample : valeur du dernier intervalle inclus dans le graphique.<br>- Max : valeur maximale des intervalles inclus dans le graphique. |
| **Deuxième graphique** | |
| Légende |Texte à afficher sous la légende pour la deuxième série. |
| Couleur |Couleur à utiliser pour les colonnes de la deuxième série. |
| Requête de graphique |Requête à exécuter pour la deuxième série.  Le décompte du nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes de graphique. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique pour la légende.<br><br>- Average : moyenne des valeurs de tous les enregistrements.<br>- Count : nombre de tous les enregistrements retournés par la requête.<br>- Last Sample : valeur du dernier intervalle inclus dans le graphique.<br>- Max : valeur maximale des intervalles inclus dans le graphique. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionner si la vérification du flux de données doit être activée pour la vignette.  Un autre message s’affiche si les données ne sont pas disponibles pour la vignette.  Cette option est généralement utilisée pour fournir un message pendant la période temporaire, lorsque la vue est installée et que des données sont disponibles. |
| Requête |Requête à exécuter pour vérifier si les données sont disponibles pour la vue.  Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message à afficher si la requête de vérification du flux de données ne retourne aucune donnée.  Si vous ne fournissez aucun message, le texte *Exécution de l'évaluation* s’affiche. |


## <a name="next-steps"></a>étapes suivantes
* En savoir plus sur la [recherche dans les journaux](log-analytics-log-searches.md) pour prendre en charge les requêtes dans les vignettes.
* Ajouter des [composants de visualisation](log-analytics-view-designer-parts.md) à votre vue personnalisée.
